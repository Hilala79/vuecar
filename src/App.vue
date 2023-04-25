<template>
  <div>
    <div id="app">

      <label><input type="radio" v-model="selectedsorttype" name="category_id" @change="onChange($event)" value="1">
        Model</label>

      <label><input type="radio" v-model="selectedsorttype" name="category_id" @change="onChange($event)" value="2"> Model
        Then Price</label>



    </div>


    <div class="row">
      <div class="col-md-8">
        <div>
          <h3>American Cars Catalog</h3>
          <table class="table">
            <thead>
              <tr>
                <th>Id</th>
                <th>Dealer</th>
                <th>Model</th>
                <th>Color</th>
                <th>Milage</th>
                <th>Price</th>
                <th>Status</th>
              </tr>
            </thead>

            <tbody>
              <tr v-for="car in carlist1" :key="car.id">
                <td>{{ car.id }}</td>
                <td>{{ car.dealer }}</td>
                <td>{{ car.model }}</td>
                <td>{{ car.color }}</td>
                <td>{{ car.milage }}</td>
                <td>{{ car.price }}</td>
                <td>{{ car.active }}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

import axios from 'axios';



export default {
  name: 'App',
  data() {
    return {
      carlist: [],
      selectedsorttype: "1",
      routeparam: "",

    }
  },
  computed: {
    carlist1: function () {
      return [...this.carlist].sort((a, b) => {
        if (a.model < b.model) return -1;
        if (a.model > b.model) return 1;
        if (this.selectedsorttype == "2") {
          console.log("price çalıştı");
          console.log("this.selectedsorttype");
          console.log(this.selectedsorttype);
          console.log(this.carlist);
          if (parseInt(a.price) < parseInt(b.price)) return -1;
          if (parseInt(a.price) > parseInt(b.price)) return 1;
        }
        return 0;
      })
        ;

    },

  },


  mounted() {
    this.links();
    console.log(this.routeparam);
    if (this.routeparam == null || this.routeparam == '') {
      this.getAllCars();
    }
    else {
      this.getAllCarsByDealer();
    }

  },
  methods: {

    getAllCarsByDealer() {
      console.log("getAllCarsByDealer");
      console.log(this.routeparam);

      axios.get('https://carworld.azurewebsites.net/CarCatalog/GetCarByDealer/' + (this.routeparam)).then((response) => {


        console.log(response);
        this.formatPostDetails(response.data);
      }).catch(error => {
        console.log(error)
      })
    },
    onChange(event) {

      this.selectedsorttype = event.target.value;
      console.log("this.selectedsorttype");
      console.log(this.selectedsorttype);

    },
    getAllCars() {
      console.log("getAllCars");
      console.log(this.routeparam);

      axios.get('https://carworld.azurewebsites.net/CarCatalog/GetAllCars').then((response) => {


        console.log(response);
        this.formatPostDetails(response.data);
      }).catch(error => {
        console.log(error)
      });
    },
    formatPostDetails(cars) {
      for (let key in cars) {
        this.carlist.push({ ...cars[key], id: key });
      }

    },


    links() {
      let url = new URL(window.location.href);

      let param = url.searchParams.get("dealer");
      this.routeparam = param;
    },
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
