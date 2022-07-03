# Postman hobbi projektek

## 1. SpaceX REST API

### 1.1 A projekt otlete es celja

A hobbi projekt otletet az [Open Source SpaceX REST API](https://github.com/r-spacex/SpaceX-API) adta. Cel: elso korben alap tesztek irasa az emlitett open source projekthez.

> I am not affiliated, associated, authorized, endorsed by, or in any way officially connected with Space Exploration Technologies Corp (SpaceX), or any of its subsidiaries or its affiliates. The names SpaceX as well as related names, marks, emblems and images are registered trademarks of their respective owners.

### 1.2 Task lista

A tesztek alapjat kepezo dokumentaciok forrasa elerheto [ide kattintva](https://github.com/r-spacex/SpaceX-API/tree/master/docs).

- [x] Company
  - [x] Get all company info
- [x] Capsules
  - [x] Get all capsules
  - [x] Get one capsule
  - [x] Query capsules
- [x] Cores
  - [x] Get all cores
  - [x] Get one core
  - [x] Query cores
- [x] Crew
  - [x] Get all crew
  - [x] Get one crew member
  - [x] Query crew members
- [x] Dragons
  - [x] Get all Dragons
  - [x] Get one Dragon
  - [x] Query Dragons
- [x] History
  - [x] Get all history events
  - [x] Get one history event
  - [x] Query history events
- [x] Landpads
  - [x] Get all landing pads
  - [x] Get one landing pad
  - [x] Query landing pads
- [x] Launchpads
  - [x] Get all launchpads
  - [x] Get one launchpad
  - [x] Query launchpads
- [x] Payloads
  - [x] Get all payloads
  - [x] Get one payload
  - [x] Query payloads
- [x] Roadster
  - [x] Get roadster info
  - [x] Query roadster
- [x] Rockets
  - [x] Get all rockets
  - [x] Get one rocket
  - [x] Query rockets
- [x] Ships
  - [x] Get all ships
  - [x] Get one ship
  - [x] Query ships
- [x] Starlink
  - [x] Get all Starlink satellites
  - [x] Get one Starlink satellite
  - [x] Query Starlink satellites
- [ ] Launches
  - [ ] Get all launches
  - [ ] Get latest launch
  - [ ] Get next launch
  - [ ] Get one launch
  - [ ] Get all past launches
  - [ ] Query launches
- [ ] Egyeb
  - [ ] Schema validaciok beepitese
  - [ ] Tesztek bovitese

### 1.3 Peldak

#### 1.3.1 Alap ellenorzesek

```javascript
// ...

pm.test("Status code is 200", () => {
    pm.response.to.have.status(200);
});

pm.test("Status code should be OK", () => {
    pm.expect(pm.response.status.toUpperCase()).to.eql("OK");
});

pm.test("Content-Type header is application/json; charset=utf-8", () => {
    pm.expect(pm.response.headers.get("Content-Type")).to.eql("application/json; charset=utf-8");
});

pm.test("Response type is JSON", () => {
    pm.response.to.be.json;
});

// ...
```

#### 1.3.2 Get all capsules

```javascript
// Alap ellenorzesek
// ...
const CAPSULE_ID = "5e9e2c5bf3591880643b2669";

pm.test(`Check ${CAPSULE_ID} capsule is exists`, () => {
    const response = pm.response.json();

    let is_capsule_exists = false;
    let capsule_index     = -1;
    
    for (let index = 0; index < response.length; index++) {
        if (response[index].id === CAPSULE_ID) {
            is_capsule_exists = true;
            capsule_index     = index;
            break;
        }
    }

    pm.expect(is_capsule_exists).be.true;
    pm.expect(capsule_index).to.gte(0);
    pm.expect(response[capsule_index].id).to.eql(CAPSULE_ID);

    pm.collectionVariables.set("capsuleID", CAPSULE_ID);
});
```

#### 1.3.3. Get one capsule

```javascript
// Alap ellenorzesek...
// ...
let capsuleIDValue = pm.collectionVariables.get("capsuleID");

pm.test(`Check ${capsuleIDValue} capsule data`, () => {
    const response = pm.response.json();

    pm.expect(response.id).to.eql(capsuleIDValue);
    pm.expect(response.reuse_count).to.eql(2);
    pm.expect(response.water_landings).to.eql(3);
    pm.expect(response.land_landings).to.eql(0);

    let expect_launches = ["5eb87ce7ffd86e000604b33b", "5eb87d03ffd86e000604b352", "5eb87d39ffd86e000604b37e"];
    for (let index = 0; index < expect_launches.length; index++) {
        pm.expect(response.launches[index]).to.eql(expect_launches[index]);    
    }

    pm.expect(response.serial).to.eql("C106");
    pm.expect(response.status).to.eql("active");
    pm.expect(response.type).to.eql("Dragon 1.1");

    pm.collectionVariables.set("capsuleID", "undefined");
});
```

#### 1.3.4 Query capsules

##### 1.3.4.1 Body tartalma

```json
{
  "query": { "status": { "$eq": "retired"}, "water_landings": { "$eq": 1}},
  "options": {}
}
```

##### 1.3.4.2 Kodreszlet

```javascript
// "query": { "status": { "$eq": "retired"}, "water_landings": { "$eq": 1}}
pm.test("Query capsules", () => {
    const response = pm.response.json();

    let capsule_count = 0;
    for(let index = 0; index < response.docs.length; index++) {
        if (response.docs[index].status === "retired" && response.docs[index].water_landings === 1) {
            capsule_count++;
        }    
    }

    pm.expect(capsule_count).to.eq(response.totalDocs);
});
```
