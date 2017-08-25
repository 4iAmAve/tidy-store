# Tidy-Store

Returns a Promise which compares a list of version tags with the one you pass in.
Stale versions get purged helping you to maintain a clean application state.

Once the promise is fulfilled, you can call the redux/createStore configuration without fearing
incompatible states due to schema changes.

![Alt text](https://g.gravizo.com/source/svg/tidy-store-puml?https%3A%2F%2Fraw.githubusercontent.com%2Fiilei%2Ftidy-store%2Fmaster%2FREADME.md)
<details> 
<summary></summary>
tidy-store-puml @startuml;
skinparam ParticipantPadding 20;
skinparam BoxPadding 10;
database AppState;
database Versions;
actor Client;
participant Release;

== Build Tools ==;

Release --> Release: define versionHash by glob-hashing\n    storage-related files / folders;

note over Release %238fe68f: versionHash="8fe68f";

alt changes in storage-related files / folders;
    rnote over Release %23fff: versionHash inferred from\ncontents of storage-related files\ne.g.:;
    note over Release %23ffd18a: versionHash="ffd18a";
    note over Release %23a3e8f9: versionHash="a3e8f9";
    rnote over Release %23fff: etc pp. Also considers a \'".version\'" file;
|||;
else no changes in storage-related files / folders;
|||;
note over Release %238fe68f: versionHash="8fe68f";
|||;
end;

==  ==;

Client -> Release: GET /;

activate Client;

Release --> Client: Bundle contains\nversionHash;

== TidyStorage Promise ==;

Client -> Versions: getVersions %28 %29;
Versions --> Client: versions;

Client -> Client: determineStaleVersions %28 %29\nreturns versions array\nwithout versionHash;

alt staleVersions empty;
|||;
else staleVersions not empty;
|||;
   Client -> Client: resetStaleVersions;
   Client -> Versions: clearVersionStorage %28 %29;
   Client -> AppState: clearAppStorage %28 %29;
end;

Client -> Versions: setVersion %28 versionHash %29;

deactivate Client;

== TidyStorage Promise fulfilled ==;

@enduml; tidy-store-puml
</details>
