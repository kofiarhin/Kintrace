# KinTrace Product Requirements Document

## 1. Product Overview

**Product:** KinTrace  
**Type:** Genealogy / family relationship research application  
**Stage:** MVP  
**Persistence:** Browser localStorage  
**Authentication:** None  
**Primary external genealogy source:** FamilySearch API  
**Discovery model:** User-triggered only

KinTrace helps researchers start with any person, build what they already know about that person's family, search for missing relatives using genealogy records, review supporting evidence, and progressively construct an understandable family network.

KinTrace is not centred around the person operating the application. A family tree may begin with any historical or living person.

## 2. Problem

Family history information is often fragmented across relatives, memories, handwritten notes, certificates, historical records, genealogy databases, and incomplete family trees.

Researchers may know several people in a family but not understand how everyone connects. Finding missing relatives often requires searching historical records, comparing similar people, determining whether records represent the same person, identifying parents/spouses/children, calculating extended relationships, and documenting why a connection is believed to be correct.

**Core problem:** It is difficult to turn fragmented information about people into a trustworthy, understandable family network.

## 3. Product Vision

KinTrace should make reconstructing a family feel like progressive discovery rather than database administration.

> Start with anyone. Build what you know. Trace what you don't.

The application combines manual family-tree construction with evidence-backed genealogy discovery.

## 4. Primary User Outcome

A researcher can:

1. create a family tree around any starting person;
2. manually add known relatives;
3. select any person in the tree;
4. search external genealogy records for missing connections;
5. review candidate matches and supporting evidence;
6. accept or reject discoveries;
7. expand the confirmed family graph;
8. understand relationships between people in the tree.

## 5. Core Product Principles

### 5.1 Tree-centric, not user-centric

The application does not assume the researcher belongs to the family.

Avoid user-relative concepts such as "Me", "My father", or "My mother" unless they genuinely describe the selected person's relationship.

Use language such as:

- Add parent
- Add child
- Add sibling
- Add spouse
- Find parents
- Find relatives
- Centre on this person

### 5.2 Evidence before certainty

KinTrace must distinguish between:

- confirmed information;
- probable information;
- possible information;
- conflicting information;
- rejected information.

External search results must never silently become confirmed members of the family tree.

### 5.3 Discovery is user-triggered

KinTrace does not automatically search genealogy services while the researcher works.

The researcher explicitly starts discovery from a selected person:

```text
Select Samuel Mensah
        ↓
Find relatives
        ↓
Choose what to investigate
        ↓
Search external source
```

This keeps research intentional and reviewable.

### 5.4 The graph determines relationships

KinTrace persists primitive relationships such as parent/child and spouse relationships.

Relationships such as sibling, grandparent, aunt, uncle, first cousin, second cousin, and first cousin once removed should be derived from the graph rather than redundantly stored wherever possible.

## 6. Target User

The primary user is someone researching a family's history.

They may be:

- researching their own family;
- researching another family;
- documenting historical genealogy;
- helping another person construct a tree;
- reconstructing information from family documents.

The MVP does not require the researcher to be represented in the tree.

## 7. MVP User Journey

### Step 1 — Launch KinTrace

KinTrace loads persisted application data from localStorage.

If a tree exists, open the existing tree. If no tree exists, show:

```text
Create a family tree

Start with a person you know.

[Add starting person]
```

### Step 2 — Add the starting person

Supported information:

- first name;
- middle name(s), optional;
- surname;
- previous/maiden surname, optional;
- sex;
- birth date or approximate year;
- birthplace;
- living/deceased status.

The person becomes the initial display root. The root has no special genealogical meaning and may later be changed.

### Step 3 — Add known relatives

From the starting person:

```text
+ Parent
+ Spouse
+ Sibling
+ Child
```

KinTrace persists each person and direct relationship locally.

### Step 4 — Explore the family tree

The main Tree screen displays the confirmed family graph.

The researcher can:

- pan;
- zoom;
- select people;
- centre on a person;
- expand/collapse branches;
- add relatives;
- edit people;
- start discovery.

### Step 5 — Select a person

Selecting a person opens their detail panel showing:

- name;
- birth/death information;
- parents;
- spouse(s);
- children;
- evidence count;
- missing information.

Actions:

- Edit
- Add relative
- Find relatives

### Step 6 — Trigger discovery

The user chooses **Find relatives** and selects what to investigate:

- Parents
- Spouse
- Children
- Other records

KinTrace creates a genealogy search using everything already known about the selected person, including name, approximate birth year, birthplace, spouse, parents, children, and known residences where available.

### Step 7 — Connect FamilySearch

If FamilySearch has not been connected, KinTrace requests the external connection when the user first performs a search.

This is an integration connection, not KinTrace authentication.

### Step 8 — Show candidate results

External results are displayed as candidates and are not automatically inserted into the tree.

Example:

```text
Possible match

Samuel Mensah
Born 1907
Kumasi

Match confidence: 93%

✓ Name matches
✓ Birthplace matches
✓ Known child matches
◐ Birth year differs by two years

[Review]
```

### Step 9 — Review evidence

The researcher sees a comparison between known information and the external record.

Actions:

```text
[Confirm match]
[Not the same person]
[Not sure]
```

### Step 10 — Confirm discovery

When the researcher confirms a candidate:

- the external record is attached as evidence;
- missing confirmed information may be added;
- new relative candidates may be created;
- the confirmed graph changes only where explicitly accepted.

### Step 11 — Continue tracing

The newly added person becomes another valid research target.

```text
Select person
    ↓
Find relatives
    ↓
Search
    ↓
Review candidates
    ↓
Confirm
    ↓
Tree expands
```

## 8. Main Screens

### 8.1 Tree

Primary application screen.

Responsibilities:

- display confirmed family graph;
- allow pan and zoom;
- select a person;
- add relatives;
- recentre the graph;
- start discovery;
- expose basic relationship information.

### 8.2 Person Detail

Displayed as a side panel from the tree.

Shows:

- name;
- photo placeholder where supported;
- birth/death information;
- parents;
- spouse(s);
- children;
- evidence count;
- missing information.

Actions:

- edit person;
- add relative;
- find relatives;
- open full profile.

### 8.3 Person Profile

Detailed historical view for a person.

Suggested sections:

- Overview
- Life
- Family
- Records
- Sources

The Life section displays a chronological timeline.

### 8.4 Discover

Stores pending genealogy findings that have not yet become confirmed tree data.

Categories may include:

- strong candidates;
- possible matches;
- unresolved findings;
- conflicts.

### 8.5 Search

Allows researchers to manually search genealogy data.

Fields may include:

- first name;
- surname;
- approximate birth year;
- birthplace;
- known relative;
- expected relationship.

### 8.6 Records

Central evidence library for linked historical records.

Supported categories may include:

- birth;
- marriage;
- death;
- census;
- other genealogy records.

### 8.7 Settings

MVP settings:

- FamilySearch connection;
- export tree;
- import tree;
- reset tree;
- default tree root;
- default generation depth.

No account-management settings are required.

## 9. Family Tree Data Model

### FamilyTree

```ts
interface FamilyTree {
  id: string;
  name: string;
  rootPersonId: string;
  createdAt: string;
  updatedAt: string;
}
```

### Person

```ts
interface Person {
  id: string;

  names: {
    first: string;
    middle?: string;
    surname: string;
    previousSurname?: string;
  };

  sex?: "male" | "female" | "unknown";

  birth?: {
    date?: string;
    approximate?: boolean;
    place?: string;
  };

  death?: {
    date?: string;
    approximate?: boolean;
    place?: string;
  };

  isLiving?: boolean;

  externalIds?: {
    familySearch?: string;
  };

  createdAt: string;
  updatedAt: string;
}
```

### Relationship

Persist only primary graph relationships.

```ts
interface Relationship {
  id: string;

  personAId: string;
  personBId: string;

  type:
    | "parent"
    | "spouse";

  status:
    | "confirmed"
    | "probable"
    | "possible";

  evidenceIds: string[];

  createdAt: string;
  updatedAt: string;
}
```

### Evidence

```ts
interface Evidence {
  id: string;

  source:
    | "user"
    | "familysearch"
    | "other";

  type:
    | "birth"
    | "marriage"
    | "death"
    | "census"
    | "relationship"
    | "other";

  externalRecordId?: string;

  personIds: string[];

  relationshipIds: string[];

  extractedData?: Record<string, unknown>;

  sourceUrl?: string;

  createdAt: string;
}
```

### Discovery

```ts
interface Discovery {
  id: string;

  targetPersonId: string;

  type:
    | "person_match"
    | "possible_parent"
    | "possible_spouse"
    | "possible_child"
    | "record";

  confidence?: number;

  status:
    | "pending"
    | "accepted"
    | "rejected"
    | "unsure";

  externalData: Record<string, unknown>;

  evidenceIds: string[];

  createdAt: string;
}
```

## 10. Persistence

MVP data is persisted entirely in browser localStorage.

Suggested root state:

```ts
interface KinTraceState {
  version: 1;

  tree: FamilyTree | null;

  persons: Person[];

  relationships: Relationship[];

  evidence: Evidence[];

  discoveries: Discovery[];
}
```

Suggested key:

```ts
kintrace:v1
```

Every successful mutation should persist automatically. There should be no global Save button.

## 11. Import and Export

Because localStorage is not a reliable backup mechanism, MVP must provide data export and import.

### Export

Export a versioned KinTrace JSON file containing:

- tree;
- persons;
- relationships;
- evidence;
- discoveries.

### Import

KinTrace must:

1. read the file;
2. validate the schema;
3. verify the supported version;
4. reject malformed data;
5. require confirmation before replacing existing local data.

## 12. Relationship Engine

The application should calculate extended relationships from parent-child graph structure.

Required MVP operations:

```ts
getParents(personId)
getChildren(personId)
getSiblings(personId)
getGrandparents(personId)
getAuntsAndUncles(personId)
getFirstCousins(personId)
findCommonAncestor(personAId, personBId)
findRelationship(personAId, personBId)
```

Inferred relationships should not require duplicate persistence.

## 13. Relationship Explorer

The researcher can select two people and ask how they are related.

KinTrace traverses the graph and returns:

- relationship name;
- path between the people;
- common ancestor where relevant.

The relationship itself must be computed deterministically from the graph.

## 14. Identity Resolution

Genealogy records frequently contain naming and date variations.

Candidate matching should consider:

- name similarity;
- birth date;
- birthplace;
- spouse;
- parents;
- children;
- residence;
- historical chronology.

Confidence scores support human review; they do not independently confirm identity.

## 15. AI Responsibilities

AI is optional infrastructure supporting research rather than the source of genealogical truth.

Appropriate uses include:

- match explanation;
- historical record extraction;
- conflict explanation.

AI must not:

- silently create people;
- silently merge identities;
- invent family relationships;
- override deterministic relationship calculations;
- treat unsupported assumptions as facts.

## 16. External Data Integration

### FamilySearch

FamilySearch is the initial genealogy data source.

Expected uses:

- person search;
- family-tree relationships;
- historical records;
- pedigree data;
- parent/child data;
- spouse relationships.

Integration should be encapsulated behind a provider abstraction so KinTrace is not permanently coupled to FamilySearch.

```ts
interface GenealogyProvider {
  searchPeople(query: PersonSearchQuery): Promise<PersonCandidate[]>;

  getPerson(id: string): Promise<ExternalPerson>;

  getRelationships(id: string): Promise<ExternalRelationship[]>;

  getRecords(id: string): Promise<ExternalRecord[]>;
}
```

Future providers may include other genealogy archives or services.

**Unresolved before implementation:** confirm FamilySearch production access, commercial-use/licensing constraints, OAuth requirements, and what retrieved data KinTrace is permitted to persist in localStorage.

## 17. Privacy

KinTrace may contain information about living people.

For the MVP:

- data remains in the researcher's browser;
- there are no public profiles;
- there is no tree sharing;
- there is no collaboration;
- there is no central KinTrace user database.

Living/deceased status should still be represented in the data model to support future privacy controls.

## 18. MVP Functional Requirements

The MVP must allow the researcher to:

- create a family tree;
- add any person as its starting person;
- add parents;
- add children;
- add spouses;
- add siblings through appropriate graph relationships;
- edit person information;
- remove incorrect manually entered relationships;
- navigate an interactive tree;
- select any person;
- recentre the tree on any person;
- search FamilySearch manually;
- review candidate matches;
- confirm or reject discoveries;
- attach evidence to people and relationships;
- calculate common family relationships;
- persist data in localStorage;
- export the complete local tree;
- import a valid KinTrace tree;
- reset the local tree.

## 19. Non-Goals for MVP

The MVP will not include:

- KinTrace user authentication;
- KinTrace user accounts;
- cloud persistence;
- multi-device syncing;
- collaboration;
- family invitations;
- public profiles;
- DNA matching;
- automatic/background genealogy searches;
- autonomous relationship creation;
- Ancestry integration;
- Findmypast integration;
- GRO scraping;
- native mobile applications;
- social features;
- genealogy messaging;
- image restoration;
- automatic photo recognition;
- production-grade GEDCOM interoperability.

## 20. Success Criteria

The MVP is successful when a researcher can:

1. create a tree around an arbitrary person;
2. manually construct known immediate family relationships;
3. close and reopen the application without losing the tree;
4. select an existing person and explicitly search for missing family information;
5. inspect multiple candidate genealogy matches;
6. understand why a candidate may match;
7. accept a discovery and see the tree expand correctly;
8. reject a discovery without modifying the confirmed tree;
9. select two confirmed people and determine their family relationship;
10. export and restore their research data.

## 21. Primary Product Loop

```text
Create tree
     ↓
Add known people
     ↓
Explore family graph
     ↓
Select a person
     ↓
Find relatives
     ↓
Search genealogy source
     ↓
Review candidates
     ↓
Confirm or reject
     ↓
Tree expands
     ↓
Repeat
```

This loop is the central experience KinTrace should optimise.

## 22. Recommended MVP Navigation

```text
Tree
Discover
Records
Search
Settings
```

The Tree remains the primary screen.

## 23. Future Expansion

Potential later phases include:

- cloud persistence;
- user accounts;
- multiple trees;
- shared family trees;
- collaboration;
- GEDCOM import/export;
- additional genealogy providers;
- automatic discovery suggestions;
- notifications;
- DNA integrations;
- record-image analysis;
- family photos;
- stories and oral-history capture;
- source-quality scoring;
- map-based family migration visualisation;
- mobile applications.

These should not influence MVP implementation unless needed to avoid an obvious architectural dead end.

## 24. Product Positioning

**KinTrace**

> Start with anyone. Build what you know. Trace what you don't.

KinTrace is a genealogy research application that helps people reconstruct a family network from incomplete information, verify discoveries using historical evidence, and understand how everyone connects.
