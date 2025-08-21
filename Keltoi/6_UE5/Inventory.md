```
// Fill out your copyright notice in the Description page of Project Settings.

#include "Inventory/InventoryComponent.h"
#include "Item/ItemObject.h"

DEFINE_LOG_CATEGORY(LogKeltoi);

// Sets default values for this component's properties
UInventoryComponent::UInventoryComponent()
{
	// Set this component to be initialized when the game starts, and to be ticked every frame.
	// You can turn these features off to improve performance if you don't need them.
	PrimaryComponentTick.bCanEverTick = true;
}

// Called when the game starts
void UInventoryComponent::BeginPlay()
{
	Super::BeginPlay();
	
	// Initialize inventory with empty slots
	ItemObjects.SetNum(Columns * Rows);
	InitializeFreeSlots();
}

// Called every frame
void UInventoryComponent::TickComponent(float DeltaTime, ELevelTick TickType, FActorComponentTickFunction* ThisTickFunction)
{
	Super::TickComponent(DeltaTime, TickType, ThisTickFunction);
	
	// Broadcast inventory change event if needed
	if (bDirtyInventory)
	{
		bDirtyInventory = false;
		OnInventoryChanging.Broadcast();
	}
}

void UInventoryComponent::TryAddItem(UItemObject* ItemToAdd, bool& bAdded)
{
	bAdded = false;
	
	if (!ItemToAdd)
	{
		return;
	}
	
	// Calculate required space for the item
	const int32 RequiredSlots = ItemToAdd->GetItemStructure().ItemDimensionX * 
	                           ItemToAdd->GetItemStructure().ItemDimensionY;
	
	// Check if we have enough free space
	UpdateFreeSlotMap();
	if (FreeMap.Num() == 0)
	{
		UE_LOG(LogKeltoi, Warning, TEXT("Inventory full"));
		return;
	}
	
	if (RequiredSlots > FreeMap.Num())
	{
		UE_LOG(LogKeltoi, Warning, TEXT("Not enough space"));
		return;
	}
	
	// Sort free slots for optimal placement
	FreeMap.KeySort([](const int32 A, const int32 B) { return A < B; });
	
	// Try to place item in available spaces
	if (TryDirectPlacement(ItemToAdd, bAdded))
	{
		return;
	}
	
	// If direct placement failed, try rearranging inventory
	if (RequiredSlots <= FreeMap.Num())
	{
		UE_LOG(LogKeltoi, Warning, TEXT("There is still place available"));
		TryRearrangementPlacement(ItemToAdd, RequiredSlots, bAdded);
	}
	
	if (!bAdded)
	{
		ResetTemporaryData();
	}
}

bool UInventoryComponent::TryDirectPlacement(UItemObject* ItemToAdd, bool& bAdded)
{
	// Loops only through each free index, if there is enough space it will add the item
	for (const auto& FreeSlot : FreeMap)
	{
		const int32 TopLeftIndex = FreeSlot.Key;
		
		// Check if there's room for the item at that index
		if (HasRoomForItem(ItemToAdd, TopLeftIndex))
		{
			AddItemAtIndex(ItemToAdd, TopLeftIndex);
			bAdded = true;
			UpdateFreeSlotMap();
			return true;
		}
	}
	
	return false;
}

void UInventoryComponent::TryRearrangementPlacement(UItemObject* ItemToAdd, int32 RequiredSlots, bool& bAdded)
{
	// Find possible placements that might work with rearrangement
	AnalyzeAvailableSpace(ItemToAdd);
	
	// Try each potential placement option
	for (auto& CandidateIndex : PotentialPlacementIndices)
	{
		if (TryRearrangementAtIndex(ItemToAdd, CandidateIndex, bAdded))
		{
			return;
		}
	}
}

bool UInventoryComponent::TryRearrangementAtIndex(UItemObject* ItemToAdd, int32 StartIndex, bool& bAdded)
{
	int32 CurrentIndex = StartIndex;
	AffectedIndices.Empty();
	RemovedItems.Empty();
	
	// Collect indices of affected slots
	AffectedIndices.Add(CurrentIndex);
	while (CurrentIndex < ItemObjects.Num() - 1)
	{
		CurrentIndex += Rows;
		if (CurrentIndex < ItemObjects.Num() - 1)
		{
			AffectedIndices.Add(CurrentIndex);
		}
	}
	
	// Collect and remove existing items
	for (auto& Index : AffectedIndices)
	{
		bool bValid;
		UItemObject* ExistingItem;
		GetItemAtIndex(Index, bValid, ExistingItem);
		
		if (!bValid)
		{
			return false;
		}
		
		if (ExistingItem)
		{
			// Store the item's original position
			int32 OriginalIndex = GetItemTopLeftIndex(ExistingItem, Index);
			RemovedItems.Add(ExistingItem, OriginalIndex);
			RemoveItem(ExistingItem);
		}
	}
	
	// Try to place the new item
	if (HasRoomForItem(ItemToAdd, StartIndex))
	{
		// Add the new item
		AddItemAtIndex(ItemToAdd, StartIndex);
		
		// Try to restore removed items
		if (RestoreRemovedItems())
		{
			bAdded = true;
			UpdateFreeSlotMap();
			ResetTemporaryData();
			return true;
		}
		else
		{
			// Restore original state
			RemoveItem(ItemToAdd);
			RestoreOriginalItems();
			UpdateFreeSlotMap();
			ResetTemporaryData();
		}
	}
	
	return false;
}

bool UInventoryComponent::RestoreRemovedItems()
{
	int32 RestoredCount = 0;
	
	for (auto& RemovedItem : RemovedItems)
	{
		UItemObject* ItemToRestore = RemovedItem.Key;
		
		// Try to find space for this item
		for (auto& FreeSlot : FreeMap)
		{
			const int32 TopLeftIndex = FreeSlot.Key;
			
			if (HasRoomForItem(ItemToRestore, TopLeftIndex))
			{
				AddItemAtIndex(ItemToRestore, TopLeftIndex);
				RestoredCount++;
				break;
			}
		}
	}
	
	return RestoredCount == RemovedItems.Num();
}

void UInventoryComponent::RestoreOriginalItems()
{
	for (auto& RemovedItem : RemovedItems)
	{
		UItemObject* ItemToRestore = RemovedItem.Key;
		int32 OriginalIndex = RemovedItem.Value;
		
		if (ItemToRestore)
		{
			AddItemAtIndex(ItemToRestore, OriginalIndex);
		}
	}
}

bool UInventoryComponent::HasRoomForItem(UItemObject* ItemToAdd, int32 TopLeftIndex)
{
	int32 TileX = IndexToTile(TopLeftIndex).X;
	int32 TileY = IndexToTile(TopLeftIndex).Y;
	
	const int32 DimensionX = ItemToAdd->GetItemStructure().ItemDimensionX;
	const int32 DimensionY = ItemToAdd->GetItemStructure().ItemDimensionY;
	
	// Check all tiles the item would occupy
	for (int32 X = TileX; X < TileX + DimensionX; ++X)
	{
		for (int32 Y = TileY; Y < TileY + DimensionY; ++Y)
		{
			const FIntPoint Tile{ X, Y };
			
			// Check if tile is within inventory bounds
			if (!IsTileValid(Tile))
			{
				UE_LOG(LogKeltoi, Warning, TEXT("Index %i Tile (%i,%i) not valid"), TopLeftIndex, X, Y);
				return false;
			}
			
			// Check if tile is occupied
			const int32 TileIndex = TileToIndex(Tile);
			bool bValid;
			UItemObject* ExistingItem;
			GetItemAtIndex(TileIndex, bValid, ExistingItem);
			
			if (!bValid)
			{
				UE_LOG(LogKeltoi, Warning, TEXT("%i is not a valid index"), TileIndex);
				return false;
			}
			
			if (ExistingItem)
			{
				UE_LOG(LogKeltoi, Warning, TEXT("%s is already present at index %i"), 
				       *ExistingItem->GetName(), TileIndex);
				return false;
			}
		}
	}
	
	return true;
}

void UInventoryComponent::AnalyzeAvailableSpace(UItemObject* ItemToAdd)
{
	PotentialPlacementIndices.Empty();
	ResultMap.Empty();
	
	// Analyze all possible placements
	for (int32 Index = 0; Index < ItemObjects.Num(); Index++)
	{
		AssessPlacementViability(ItemToAdd, Index);
	}
	
	// Group results by status
	TMap<int32, int32> OkSlots;
	TMap<int32, int32> BusySlots;
	TMap<int32, int32> InvalidSlots;
	
	// Count slot statuses for each potential placement
	for (const auto& Result : ResultMap)
	{
		int32 Index = Result.Key;
		FString Status = Result.Value;
		
		if (Status == TEXT("OK"))
		{
			OkSlots.FindOrAdd(Index)++;
		}
		else if (Status == TEXT("Busy"))
		{
			BusySlots.FindOrAdd(Index)++;
		}
		else
		{
			InvalidSlots.FindOrAdd(Index)++;
		}
	}
	
	// Find indices that might work with rearrangement
	const int32 RequiredSlots = ItemToAdd->GetItemStructure().ItemDimensionX * 
	                           ItemToAdd->GetItemStructure().ItemDimensionY;
	
	for (int32 Index = 0; Index < ItemObjects.Num(); Index++)
	{
		// Skip locations with invalid slots
		if (InvalidSlots.Contains(Index))
		{
			continue;
		}
		
		int32 OkCount = OkSlots.Contains(Index) ? OkSlots[Index] : 0;
		int32 BusyCount = BusySlots.Contains(Index) ? BusySlots[Index] : 0;
		
		// If all required slots are either available or can be made available
		if (OkCount + BusyCount == RequiredSlots)
		{
			PotentialPlacementIndices.Add(Index);
		}
	}
}

void UInventoryComponent::AssessPlacementViability(UItemObject* ItemToAdd, int32 TopLeftIndex)
{
	int32 TileX = IndexToTile(TopLeftIndex).X;
	int32 TileY = IndexToTile(TopLeftIndex).Y;
	
	const int32 DimensionX = ItemToAdd->GetItemStructure().ItemDimensionX;
	const int32 DimensionY = ItemToAdd->GetItemStructure().ItemDimensionY;
	
	// Assess each tile the item would occupy
	for (int32 X = TileX; X < TileX + DimensionX; ++X)
	{
		for (int32 Y = TileY; Y < TileY + DimensionY; ++Y)
		{
			const FIntPoint Tile{ X, Y };
			
			// Check if tile is within inventory bounds
			if (!IsTileValid(Tile))
			{
				ResultMap.Add(TopLeftIndex, "Invalid");
				continue;
			}
			
			// Check if tile is occupied
			const int32 TileIndex = TileToIndex(Tile);
			bool bValid;
			UItemObject* ExistingItem;
			GetItemAtIndex(TileIndex, bValid, ExistingItem);
			
			if (ExistingItem)
			{
				ResultMap.Add(TopLeftIndex, "Busy");
			}
			else if (bValid)
			{
				ResultMap.Add(TopLeftIndex, "OK");
			}
		}
	}
}

void UInventoryComponent::AddItemAtIndex(UItemObject* ItemToAdd, int32 TopLeftIndex)
{
	int32 TileX = IndexToTile(TopLeftIndex).X;
	int32 TileY = IndexToTile(TopLeftIndex).Y;
	int32 DimX = ItemToAdd->GetDimensions().X;
	int32 DimY = ItemToAdd->GetDimensions().Y;
	
	for (int32 X = TileX; X < TileX + DimX; ++X)
	{
		for (int32 Y = TileY; Y < TileY + DimY; ++Y)
		{
			FIntPoint Tile{ X, Y };
			int32 SlotIndex = TileToIndex(Tile);
			
			ItemObjects[SlotIndex] = ItemToAdd;
			InventoryMap.Add(SlotIndex, Tile);
			FreeMap.Remove(SlotIndex);
		}
	}
	
	bDirtyInventory = true;
}

void UInventoryComponent::RemoveItem(UItemObject* ItemToRemove)
{
	if (!ItemToRemove)
	{
		return;
	}
	
	for (int32 Index = 0; Index < ItemObjects.Num(); ++Index)
	{
		if (ItemToRemove == ItemObjects[Index])
		{
			ItemObjects[Index] = nullptr;
			FIntPoint Tile = IndexToTile(Index);
			FreeMap.Add(Index, Tile);
			bDirtyInventory = true;
		}
	}
	
	UpdateFreeSlotMap();
}

void UInventoryComponent::GetAllItems(TMap<UItemObject*, FIntPoint>& AllItems)
{
	TMap<UItemObject*, FIntPoint> ItemMap;
	
	for (int32 Index = 0; Index < ItemObjects.Num(); ++Index)
	{
		if (UItemObject* CurrentItem = ItemObjects[Index])
		{
			if (!ItemMap.Contains(CurrentItem))
			{
				ItemMap.Add(CurrentItem, IndexToTile(Index));
			}
		}
	}
	
	AllItems = ItemMap;
}

void UInventoryComponent::GetItemAtIndex(int32 Index, bool& bValid, UItemObject*& Item)
{
	if (ItemObjects.IsValidIndex(Index))
	{
		bValid = true;
		Item = ItemObjects[Index];
	}
	else
	{
		bValid = false;
		Item = nullptr;
	}
}

int32 UInventoryComponent::GetItemTopLeftIndex(UItemObject* Item, int32 KnownIndex)
{
	TMap<UItemObject*, FIntPoint> AllItems;
	GetAllItems(AllItems);
	
	for (const auto& ItemEntry : AllItems)
	{
		if (ItemEntry.Key == Item)
		{
			return TileToIndex(ItemEntry.Value);
		}
	}
	
	return KnownIndex; // Fallback to known index
}

void UInventoryComponent::InitializeFreeSlots()
{
	FreeMap.Empty();
	
	// Add all slots to free map
	for (int32 Column = 0; Column < Columns; ++Column)
	{
		for (int32 Row = 0; Row < Rows; ++Row)
		{
			FIntPoint Tile{ Column, Row };
			int32 SlotIndex = TileToIndex(Tile);
			FreeMap.Add(SlotIndex, Tile);
		}
	}
}

void UInventoryComponent::UpdateFreeSlotMap()
{
	// Log free slots for debugging
	for (const auto& FreeSlot : FreeMap)
	{
		const int32 SlotIndex = FreeSlot.Key;
		const FIntPoint Tile = FreeSlot.Value;
		UE_LOG(LogKeltoi, Verbose, TEXT("Free slot: %i, Tile: (%i,%i)"), SlotIndex, Tile.X, Tile.Y);
	}
}

void UInventoryComponent::ResetTemporaryData()
{
	AffectedIndices.Empty();
	RemovedItems.Empty();
	PotentialPlacementIndices.Empty();
	ResultMap.Empty();
}
```