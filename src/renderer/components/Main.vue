<template>
  <div id="wrapper">
    <main>
      <div class="container">
        <!-- Load the file -->
        <div class='loadFile'>
            <input type="file" name="spoilerdata" id="spoilerdata" accept=".json" @change='loadFile'>
            <button onClick="document.getElementById('spoilerdata').click()">Load Spoiler Log</button>
        </div>

        <!-- Spoil the location -->
        <div class = 'spoiler'>
            <div v-if="data !== null & world !== null & spoilertext !== null">
                <h1> {{spoilertext}} </h1>
            </div>
        </div>

        <!-- Choose a player -->
        <div class='players'>
            <div  v-if="data !== null">
                <div v-for="(player, id) in data">
                    <button @click="world = id"> Player {{ id }} </button>
                </div>
            </div>
        </div>

        <!-- Choose a location -->
        <div class = 'location'>
            <div  v-if="data !== null & world !== null" class="item-container">
                <div v-for="(groupData, group) in data[world]" class="item-group">
                    <h3>{{group}}</h3>
                    <div>
                        <button v-for="item in groupData" v-bind:key="item.id" @click="spoilertext = item.local + ' ['+ item.world +']'" v-if="item.item != undefined"> {{item.item}} </button>
                    </div>
                </div>
            </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
/* eslint-disable no-unused-vars */
/* eslint-disable semi */
/* eslint-disable indent */
/* eslint-disable no-unexpected-multiline */
/* eslint-disable null */
/* eslint-disable */

const parsePlayers = async (data) => {
  console.log(data)
  var tmp = {}
  await [...Array(data.settings.world_count).keys()].map(i => {
      tmp[i+1] = {
          Items: [],
          Songs: [],
          Upgrades: [],
          Swords: [],
          Shields: [],
          Tunics: [],
          Boots: [],
          Medallions: [],
          Jewels: [],
          Trade: [],
          Traps: [],
          Hearts: [],
          Maps: [],
          Compasses: [],
          Keys: [],
          BossKeys: [],
          Other: [],
        }
  })
  for (const world in data.locations) {
      for (const local in data.locations[world]) {
        //   tmp[data.locations[world][local].player][local] = {...data.locations[world][local], ...{local: local, world: world}}
        const location = data.locations[world][local]
        if (location.item.includes("Medallion")) {
            tmp[location.player].Medallions.push({...location, ...{local: local, world: world}})
        } else if (
            location.item.includes("Ruby") | 
            location.item.includes("Sapphire") | 
            location.item.includes("Emerald")
            ) {
            tmp[location.player].Jewels.push({...location, ...{local: local, world: world}})
        } else if (location.item.includes("Tunic")) {
            tmp[location.player].Tunics.push({...location, ...{local: local, world: world}})
        } else if (location.item.includes("Sword")) {
            tmp[location.player].Swords.push({...location, ...{local: local, world: world}})
        } else if (location.item.includes("Shield")) {
            tmp[location.player].Shields.push({...location, ...{local: local, world: world}})
        } else if (location.item.includes("Boot")) {
            tmp[location.player].Boots.push({...location, ...{local: local, world: world}})
        } else if (location.item.includes("Trap")) {
            tmp[location.player].Traps.push({...location, ...{local: local, world: world}})
        } else if (
            location.item.includes("Piece") | 
            location.item.includes("Container")
        ) {
            tmp[location.player].Hearts.push({...location, ...{local: local, world: world}})
        } else if (
            location.item.includes("Song") | 
            location.item.includes("Bolero") | 
            location.item.includes("Minuet") | 
            location.item.includes("Prelude") | 
            location.item.includes("Nocturne") | 
            location.item.includes("Requiem") | 
            location.item.includes("Serenade") |
            location.item.includes("Lullaby")
        ) {
            tmp[location.player].Songs.push({...location, ...{local: local, world: world}})
        } else if (
            location.item.includes("Bow") |
            location.item.includes("Slingshot") |
            location.item.includes("Bomb Bag") |
            location.item.includes("Din") |
            location.item.includes("Nayru") |
            location.item.includes("Farore") |
            location.item.includes("Hammer") |
            location.item.includes("Fire Arrow") |
            location.item.includes("Ice Arrow") |
            location.item.includes("Light Arrow") |
            location.item.includes("Lens") |
            location.item.includes("Bottle") |
            location.item.includes("Ruto") |
            location.item.includes("Ocarina") |
            location.item.includes("Hookshot") |
            location.item.includes("Boomerang") |
            location.item.includes("Stone of Agony")
        ) {
            tmp[location.player].Items.push({...location, ...{local: local, world: world}})
        } else if (
            location.item.includes("Progressive") |
            location.item.includes("Capacity") | 
            location.item.includes("Magic") |
            location.item.includes("Defense")
            ) {
            tmp[location.player].Upgrades.push({...location, ...{local: local, world: world}})
        } else if (location.item.includes("Map")) {
            tmp[location.player].Maps.push({...location, ...{local: local, world: world}})
        } else if (location.item.includes("Compass")) {
            tmp[location.player].Compasses.push({...location, ...{local: local, world: world}})
        } else if (location.item.includes("Boss Key")) {
            tmp[location.player].BossKeys.push({...location, ...{local: local, world: world}})
        } else if (location.item.includes("Small Key")) {
            tmp[location.player].Keys.push({...location, ...{local: local, world: world}})
        } else {
            tmp[location.player].Other.push({...location, ...{local: local, world: world}})
        }
      }
  }
  function compare(a, b) {
      const itemA = a.item.toUpperCase()
      const itemB = b.item.toUpperCase()
      let comparison = 0;
      if (itemA > itemB) {
        comparison = 1;
      } else if (itemA < itemB) {
        comparison = -1;
      }
      return comparison;
  }
  for (const player in tmp) {
      for (const group in tmp[player]) {
          await tmp[player][group].sort(compare)
      }
  }
  
  console.log(tmp)
  return tmp
  
}

export default {
  name: 'main',
  methods: {
    open (link) {
      this.$electron.shell.openExternal(link)
    },
    loadFile (ev) {
      const file = ev.target.files[0]
      const reader = new FileReader()
      reader.addEventListener('load', e => {
        // console.log(e.target.result, JSON.parse(reader.result))
        const data = JSON.parse(reader.result)
        parsePlayers(data).then(tmp => {
            this.data = tmp
            console.log(tmp)
        })
        
        // var tmp = {}
        // console.log([])
        // await [...Array(5).keys()].map(i => console.log(i))
      });
      reader.readAsText(file)
    }
  },
  data () {
    return {
      data: null,
      world: null,
      spoilertext: null
    }
  }
}
</script>

<style>
  .container {
      display: grid;
      grid-template-columns: 200px auto;
      grid-template-rows: 50px auto;
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background-color: blue;
      overflow: hidden;
  }

    /* Load the file */
  .loadFile {
      background-color: aqua;
  }

  .loadFile input{
      display: none;
  }

  .loadFile button {
      width: 100%;
      height: 100%;
      border: none;
      background-color: green;
      font-size: 1.2em;
  }

  .loadFile button:hover {
      color: white;
      background-color: rgb(0, 63, 0);
  }

    /* Show the players */
  .players {
      background-color: rgb(0, 85, 0);
      display: flex;
      flex-direction: column;
      overflow-x: hidden;
      overflow-y: auto;
  }

  .players button {
      width: 100%;
      height: 50px;
      border: none;
      border-bottom: 1px solid black;
      border-top: 1px solid black;
      background-color: rgb(81, 167, 1)
  }

    /* Choose a location */
  .location {
      background-color: rgb(0, 58, 0);
      overflow-x: hidden;
      overflow-y: auto;
  }

  .item-container {
      display: flex;
      flex-flow: wrap;
      flex-direction: row;
      align-items: flex-start;
      justify-content: center;
  }

  .item-group {
      display: flex;
      flex-direction: column;
      width: 25vmin;
      align-items: center;
      justify-content: center;
      margin: 2vmin;
      color: white;
  }

  .item-group button {
      width: 100%;
      border: none;
      margin-top: 1px;
      background-color: rgb(0, 34, 0);
      color:white;
      min-height: 25px;
  }

  .item-group button:hover {
      background-color: rgb(0, 53, 0);
  }

  /* .itemgroups {
      display: flex;
      width: 100%;
      flex-direction: row;
      flex-flow: wrap;
  }

  .group {
      width: 30vmin;
      height: auto;
      display: flex;
      justify-content: center;
      align-items: center;
  }

  .items {
      display: flex;
      flex-direction: row;
  }

  .items button {
      width: 100%;
  } */

    /* Show the spoiled location */
  .spoiler {
      background-color: rgb(0, 146, 0);
      display: flex;
      align-items: center;
      justify-content: center;
  }
</style>
