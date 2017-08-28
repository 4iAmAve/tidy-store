# Tidy-Store

Returns a Promise which compares a list of version tags with the one you pass in.
Stale versions get purged helping you to maintain a clean application state.

Once the promise is fulfilled, you can call the redux/createStore configuration without fearing
incompatible states due to schema changes.

![Alt text](https://g.gravizo.com/source/svg/tidy-store-puml?https%3A%2F%2Fraw.githubusercontent.com%2Fiilei%2Ftidy-store%2Fmaster%2FREADME.md)
<details> 
<summary></summary>

```
tidy-store-puml @startuml;
skinparam ParticipantPadding 20;
skinparam BoxPadding 10;
participant Build;
participant Server;
database AppState;
actor Client;

== Build Tools ==;

note over Build %238fe68f: currentVersion="8fe68f";
Build -> Server: deployment;

hnote over Build: Repository Updates;

Build --> Build: define currentVersion by glob-hashing\n    storage-related files / folders;

rnote over Build %23fff: currentVersion inferred from\ncontents of storage-related files\ne.g.:;

alt changes in storage-related files / folders;
|||;
    note over Build %23ffd18a: currentVersion="ffd18a";
    note over Build %23a3e8f9: currentVersion="a3e8f9";
    rnote over Build %23fff: etc pp. Also considers a \'".version\'" file;
    Build -> Server: deployment;
|||;
else no changes in storage-related files / folders;
|||;
    note over Build %238fe68f: currentVersion="8fe68f";
    Build -> Server: deployment;
|||;
end;

==  ==;

Client -> Server: GET /;

activate Client;

Server --> Client: Bundle contains\ncurrentVersion;

== TidyStorage Promise ==;

Client -> AppState: getVersion %28 %29;
AppState --> Client: cachedVersion;

Client -> Client: isLatestVersion?\ntrue if currentVersion === cachedVersion;

alt isLatestVersion === true;
|||;
else isLatestVersion === false;
|||;
   Client -> AppState: clearAppStorage %28 %29;
   Client -> AppState: setVersion %28 currentVersion %29;
end;
|||;

deactivate Client;

== TidyStorage Promise fulfilled ==;

@enduml; tidy-store-puml
```

</details>
