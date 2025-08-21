https://dev.epicgames.com/documentation/en-us/unreal-engine/ftext-in-unreal-engine?application_version=5.4
https://dev.epicgames.com/documentation/en-us/unreal-engine/text-localization-in-unreal-engine
https://dev.epicgames.com/documentation/en-us/unreal-engine/using-string-tables-for-text-in-unreal-engine
https://forums.unrealengine.com/t/using-string-tables-from-c/417239
https://forums.unrealengine.com/t/how-to-use-a-string-table-asset-from-c-code/463449
https://benui.ca/unreal/ui-localization/
https://benui.ca/unreal/stringtable-cpp/
https://www.youtube.com/watch?v=xcW2k72SKeg
https://www.youtube.com/watch?app=desktop&v=tfLee0Kd5jE
https://dev.epicgames.com/community/snippets/r14/format-text
https://forums.unrealengine.com/t/confusion-with-string-table-localization-c/812581/4
https://www.coconutlizard.co.uk/blog/strings-and-other-things/
https://pafuhana1213.hatenablog.com/entry/2023/01/14/153800
https://forums.unrealengine.com/t/localization-dont-work-on-dynamic-string-table-loaded-from-csv/403243/3
```
#include "Internationalization/StringTableRegistry.h"

#define LOCTEXT_NAMESPACE "MyNamespace"
		FFormatNamedArguments Args;
		FText SnippetCount = FText::FromString("Hello");
		FText GroupNameText = FText::FromString("World");
		Args.Add(TEXT("Count"), SnippetCount);
		Args.Add(TEXT("Name"), GroupNameText);

		FText Letstry = FText::Format(LOCTEXT("SnippetHeader", "There are {Count} snippets in group {Name}"), Args);

		FString SomeString = Letstry.ToString();
		UE_LOG(LogTemp, Log, TEXT("SomeString: %s"), *SomeString);
		UE_LOG(LogTemp, Warning, TEXT("Item is valid"));
#undef LOCTEXT_NAMESPACE


FString appName = LOCTABLE("/Game/_Main/Widgets/LocUI.LocUI", "MainMenu_GameTitle").ToString();

FText::Format(SpeechText, FText::FromStringTable("LocDialogue", "Variable"))

//Taken from UKismetStringTableLibrary::GetKeysFromStringTable

FStringTableConstPtr StringTable = FStringTableRegistry::Get().FindStringTable(TableId);
if (StringTable.IsValid())
{
	StringTable->EnumerateSourceStrings([&](const FString& InKey, const FString& InSourceString) -> bool
	{
		KeysFromStringTable.Add(InKey);
		return true; // continue enumeration
	});
}

```