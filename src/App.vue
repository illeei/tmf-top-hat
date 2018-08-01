<template>
  <div id="app">
    <colorLine></colorLine>
    <mainBar :serviceList="serviceList" :reportList="reportList" :uname="uname" :uid="uid"></mainBar>
  </div>
</template>

<script>
import Color from './components/Color-line.vue'
import MainBar from './components/Main-bar.vue'

export default {
  name: 'app',
  data() {
    return {
      serviceList: [],
      reportList: [],
      uname: '',
      uid: ''
    }
  },
  components: {
    colorLine: Color,
    mainBar: MainBar
  },
  created() {
    var that = this;
    const getUname = document.cookie.split('username=')[1].split('&')[0];
      this.uname = getUname;
    const getUid = document.cookie.split('uid=')[1].split('&')[0];
      this.uid = getUid;
    const api = "premium-api/accessibleServices/GetAccessibleServicesByUid?uid=" + this.uid + "&apikey=43d6f9d8-478a-4d86-abee-d1e0554cd6d6";
    const accessibleServicesPromise = this.axios.get(api)
    .then((response) => {
      return response.data;
    });
    const productListPromise = this.axios.get("ffe-api/products/products/")
    .then((response) => {
      return response.data;
    });
    this.axios.all([accessibleServicesPromise, productListPromise])
      .then(this.axios.spread(function (accessibleServices, productList) {
      for (var product of productList) {

        if ((product.product_type === "service" || product.product_type === "portfolio") && product.is_archived !== true && accessibleServices.includes(product.id)) {
          that.serviceList.push(product);
        }

        if (product.product_type === "report" && product.is_archived !== true && accessibleServices.includes(product.id) && that.reportList.length <= 4) {
          that.reportList.push(product);
        }
      }
  }));
  }
}
</script>

<style>
  body {
    margin: 0;
    height: 1200px;
  }

  #app {
    font-family: sans-serif;
  }

</style>
