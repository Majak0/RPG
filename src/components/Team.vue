<template>
  <div>
    <div style="display: flex">
      <div style="text-align: center; width: 20%">
        <!-- PersoSelector  -->
        <select @change="selectPlayer($event.target.value)">
          <option v-for="(player,index) in players" :key="index">{{player.name}}</option>
        </select>
      </div>
      <div style="text-align: center; width: 80%">
        <!-- EXO 1.2 : nom de la ville courante  -->
        <h2>{{currentTown.name}}</h2>
      </div>
    </div>

    <div style="display: flex">
      <div style="width: 50%">
        <!-- EXO 2.1 : liste à puce des rues pour sélectionner la rue courante -->
        <ul>
          <li v-for="(street,index) in currentTown.streets" :key="index">
            <input type="radio" name="street" @click="selectStreet($event.target.value)" v-bind:value="street.name">{{street.name}}
          </li>
        </ul>
      </div>
      <div v-if="currentStreet !==null" style="width: 50%">
        <!-- EXO 2.2 : liste à puce des boutiques pour sélectionner la boutique courante  -->
        <ul>
          <li v-for="(shop,index) in currentStreet.shops" :key="index">
            <input type="radio" name="shop" @click="selectShop($event.target.value)" v-bind:value="shop.name">{{shop.name}}
          </li>
        </ul>
      </div>
    </div>

    <div style="display: flex">

      <div v-if="currentPlayer !== null" style="width: 50%">
        <!-- EXO 1.3 : affiche nom du perso courant - niveau - points de vie  -->
        {{currentPlayer.name+" | niveau "+currentPlayer.level+" | vie : "+currentPlayer.vitality}}
        <table border="1">
          <!-- EXO 4.1 : affiche les slots avec les items qu'ils contiennent -->
          <tr v-for="(slot,index) in currentPlayer.slots" :key="index">
            <td>
              {{slot.name}}
            </td>
            <td v-for="(item,index) in slot.items" :key="index">
              [ {{index}} ] - {{item.name}}
            </td>
          </tr>
        </table>

        <div style="height:20px"></div>

        <div>
          <!-- EXO 4.2 : affiche un label+champ de saisie avec l'or du perso courant -->
          <input v-bind:value="currentPlayer.gold" disabled=disabled>
        </div>

        <div style="height:20px"></div>

        <div>
          <!-- EXO 4.3 : affiche label+champ de saisie avec la liste des items achetés -->
          <input type="text" readonly="readonly" :value="boughtItems">
        </div>

        <div>
          <!-- EXO 4.5 : affiche 2 x [label+champ de saisies] + bouton pour pouvoir assigner les items achetés -->
          <label :for="idxItemBought">index de l'item dans bought item : </label>
          <input type="text"  v-model="idxItemBought">
          <br>
          <label :for="idxSlotAssign">index du slot  : </label>
          <input type="text"  v-model="idxSlotAssign">
        </div>

        <div style="height:20px"></div>

        <div>
          <!-- EXO 4.7 : affiche label+champ de saisie + bouton pour pouvoir acheter un item de la boutique -->
          <label :for="idxItemBuy">index de l'item à acheté : </label>
          <input type="text"  v-model="idxItemBuy">

          <input type="submit" value="buy" @click="buy">
        </div>

        <div style="height:20px"></div>

        <div>
          <!-- EXO 4.9 : affiche label+champ de saisie + bouton pour pouvoir commander un item de la boutique -->
          <label :for="idxItemOrder">index de l'item à commander : </label>
          <input type="text"  v-model="idxItemOrder">

          <input type="submit" value="order" @click="order">
        </div>

        <div style="height:20px"></div>

        <div>
          <!-- EXO 4.11 : affiche 2 x [label+champ de saisies] + bouton pour pouvoir vendre un item assigné à un slot -->
          <label :for="idxItemSell">index de l'item à vendre dans le slot : </label>
          <input type="text"  v-model="idxItemSell">
          <br>
          <label :for="idxSlotSell">index du slot : </label>
          <input type="text"  v-model="idxSlotSell">
          <input type="submit" value="sell" @click="sell">
        </div>
      </div>

      <div v-if="currentShop !== null" style="width: 50%">
        <!-- EXO 3.1 : nom de la boutique courante -->
        <h2>{{currentShop.name}}</h2>

        <div style="display: flex">
          <div style="width: 50%">
            <h4>In stock</h4>
            <!-- EXO 3.2 : liste d'items en stock de la boutique courante -->
            <ol>
              <li v-for="(item,index) in currentShop.itemStock" :key="index">{{item.name}} : {{item.price}}€</li>
            </ol>
          </div>
          <div style="width: 50%">
            <h4>To order</h4>
            <!-- EXO 3.3 : liste d'items sur commande de la boutique courante -->
            <ol>
              <li v-for="(item,index) in currentShop.itemOrder" :key="index">{{item.name}} : {{item.price}}€</li>
            </ol>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import {team} from '../model'

console.log(team)

export default {
  name: 'Town',
  data: () => {
    return {
      players: team, // la liste des persos (permet d'observer la variable externe players, importée depuis model.js)
      currentPlayer: team[0], // le perso courant
      idxItemBuy:1, // la valeur du champ de saisie pour acheter un item
      idxItemOrder:1, // la valeur du champ de saisie pour commander un item
      idxItemBought:1, // la valeur du champ de saisie "index item" pour assigner un item
      idxSlotAssign:1, // la valeur du champ de saisie "index slot" pour assigner un item
      idxItemSell:1, // la valeur du champ de saisie "index item" pour vendre un item
      idxSlotSell:1, // la valeur du champ de saisie "index slot" pour vendre un item
    }
  },
  computed: {
    boughtItems() {
      /* EXO 4.4
         - construire txt le nom des items achetés+index
      */
      let txt = "" ;
      let index = 0;
      this.currentPlayer.boughtItems.forEach(item =>{
        txt += "[" + index + "] - " + item.name + " | ";
        index++;
      });
      return txt;
    },
  },
  methods: {
    selectPlayer(id)  {
      /* EXO 1.4
         - met à jour le joueur courant grâce à id
      */
      team.forEach(player => {
        if (id === player.name){
          this.currentPlayer = player;
        }
      });
    },
    assign() {
      /* EXO 4.6
         - comme dans le TP 2 mais en prenant l'id de l'item+slot à assigner dans idxItemBought+idxSlotAssign
      */
      this.currentPlayer.assign(this.idxItemBought,this.idxSlotAssign)
    },
    buy() {
      /* EXO 4.8
         - comme dans le TP 2 mais en prenant l'id de l'item à acheter dans idxItemBuy
      */
      this.currentPlayer.buy(this.currentShop.itemStock[this.idxItemBuy])
    },
    async order() {
      /* EXO 4.10
         - comme dans le TP 2 mais en prenant l'id de l'item à acheter dans idxItemOrder
      */
      this.currentShop.order(this.idxItemOrder);
    },
    sell() {
      /* EXO 4.12
         - comme dans le TP 2 mais en prenant l'id de l'item+slot à assigner dans idxItemSell+idxSlotSell
      */
      this.currentPlayer.sell(this.idxSlotSell, this.idxItemSell,10);
    },
  }
}
</script>