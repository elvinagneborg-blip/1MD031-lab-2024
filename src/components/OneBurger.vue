<template>

  <div class="burger">
            <h3> {{burger.name}} {{burger.kCal}} kCal</h3> 
            <img :src="burger.img" style="height: 200px; width: 300px">
            <p> Pris: {{burger.price}} kr </p>
            <ul>
                <li v-if="burger.gluten"> Inehåller gluten </li>
                <li v-else> Innehåller inte gluten </li>
                <li v-if="burger.lactose"> Inehåller laktos </li>
                <li v-else> Innehåller inte laktos </li>
            </ul>
            <p>Ingredienser:</p>
            <ul>
                <li v-for="ingredient in burger.ingredients">{{ ingredient }} </li>
            </ul>

            <div class="order-controls">
            <p>
            Antal beställda: <b>{{ amountOrdered }}</b>
            </p>
            <button v-on:click="increaseAmount">Lägg till</button>
            <button v-on:click="decreaseAmount" :disabled="amountOrdered === 0">Ta bort</button>
            </div>

        </div>

</template>

<script>
export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },
  data: function () {
    return {
      amountOrdered: 0
    }
  },
  methods: {
    increaseAmount: function() {
      this.amountOrdered += 1;
      this.$emit('update-order', { name: this.burger.name, amount: this.amountOrdered });
    },
    decreaseAmount: function() {
      if (this.amountOrdered > 0) {
        this.amountOrdered -= 1;
        this.$emit('update-order', { name: this.burger.name, amount: this.amountOrdered });
      }
    },
  }
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.burger {
     margin-left: 8px;
     margin-right: 8px;
     border: 2px dashed white;
     padding-left: 5px;
}

.burger img {
    border: 2px solid white;
    margin-left: 60px;
}

.burger h3 {
    text-align: center;
}

</style>