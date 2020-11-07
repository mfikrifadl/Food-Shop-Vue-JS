<template>
  <div class="div">
    <Navbar />
    <div class="container">
      <!-- breadcrumb -->
      <div class="row mt-5">
        <div class="col">
          <nav aria-label="breadcrumb">
            <ol class="breadcrumb">
              <li class="breadcrumb-item">
                <router-link to="/" class="text-dark">Home</router-link>
              </li>
              <li class="breadcrumb-item">
                <router-link to="/foods" class="text-dark">Foods</router-link>
              </li>
              <li class="breadcrumb-item active" aria-current="page">
                Food Order
              </li>
            </ol>
          </nav>
        </div>
      </div>

      <div class="row mt-3">
        <div class="col-md-6">
          <img
            :src="'/assets/images/' + product.gambar"
            class="img-fluid shadow"
          />
        </div>
        <div class="col-md-6">
          <h2>
            <strong>{{ product.nama }}</strong>
            <hr />
            <h4>
              Harga : <strong>Rp. {{ product.harga }}</strong>
            </h4>
            <form class="mt-4" v-on:submit.prevent>
              <div class="form-group">
                <label class="label-detail" for="jumlah_pesanan"
                  >Jumlah Pesanan</label
                >
                <input
                  type="number"
                  class="form-control"
                  v-model="pesan.jumlah_pesanan"
                  required
                />
              </div>
              <div class="form-group">
                <label class="label-detail" for="keterangan">Keterangan</label>
                <textarea
                  v-model="pesan.keterangan"
                  class="form-control"
                  placeholder="Keterangan seperti : Pedas, sedang, pakai telor"
                ></textarea>
              </div>
              <button type="submit" class="btn btn-success" @click="pemesanan">
                <b-icon-cart></b-icon-cart> Pesan
              </button>
            </form>
          </h2>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Navbar from "@/components/Navbar.vue";
import axios from "axios";
export default {
  name: "FoodDetail",
  components: {
    Navbar,
  },
  data() {
    return {
      product: {},
      pesan: {},
    };
  },
  methods: {
    setProduct(data) {
      this.product = data;
    },
    pemesanan() {
      if (this.pesan.jumlah_pesanan && this.pesan.keterangan) {
        this.pesan.products = this.product;
        axios
          .post("http://localhost:3000/keranjangs", this.pesan)
          .then(() => {
            this.$toast.success("Sukses Masuk Keranjang", {
              type: "success",
              position: "top-right",
              duration: 3000,
              dismisable: true,
            });
          })
          .catch((error) => {
            console.log("Gagal : " + error);
          });
      } else {
        this.$toast.success("Isi jumlah pesanan dan keterangan", {
          type: "error",
          position: "top-right",
          duration: 3000,
          dismisable: true,
        });
      }
    },
  },
  mounted() {
    axios
      .get("http://localhost:3000/products/" + this.$route.params.id)
      .then((response) =>
        // handle success
        this.setProduct(response.data)
      )
      .catch((error) =>
        // handle error
        console.log("Gagal : ", error)
      );
  },
};
</script>

<style>
</style>