# Rank Players by the Amount of Gold Coins
```js
import * as pr from "./PbRls.ts";
const value = api.getInventoryItemAmount(pId,"Gold Coin");    // Changes what players are ranked for
let pos = [-5, 1, 14];                                        // Changes the Position of the Nametag Leaderboard
globalThis.lbNtConfig = {
    lbNtIds: {},
    Coins: {
        getValue: (pId) => {
            const raw = value
            return Number(raw ?? 0);
        },
        sort: "highest",
        pos: pos,
        topPC: 3,
    }
};
pos = [-12.5, 4, 14.5];                                        // Changes the Position of the NPC Leaderboard
globalThis.lbStageConfig = {
    lbStageNtIds: {},
    animated: {},
    Coins: {
        trophy: "held",
        getValue: (pId) => {
            const raw = api.getInventoryItemAmount(pId,"Gold Coin");
            return Number(raw ?? 0);
        },
        sort: "highest",
        p1: pos,
        p2: [pos[0]-3, pos[1]-1, pos[2]],
        p3: [pos[0]+3, pos[1]-2, pos[2]],
    }
};

tick = () => { pr.lbTick(); }
```
