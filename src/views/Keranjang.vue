<template>
  <div class="div">
    <Navbar :updateKeranjang="keranjangs" />
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
              <li class="breadcrumb-item active" aria-current="page">Cart</li>
            </ol>
          </nav>
        </div>
      </div>

      <div class="row">
        <div class="col">
          <h2>Keranjang <strong>Saya</strong></h2>
          <div class="table-responsive mt-3">
            <table class="table">
              <thead>
                <tr>
                  <th scope="col">#</th>
                  <th scope="col">Foto</th>
                  <th scope="col">Makanan</th>
                  <th scope="col">Keterangan</th>
                  <th scope="col">Jumlah</th>
                  <th scope="col">Harga</th>
                  <th scope="col">Total Harga</th>
                  <th scope="col">Hapus</th>
                </tr>
              </thead>
              <tbody>
                <tr
                  v-for="(keranjang, index) in keranjangs"
                  :key="keranjang.id"
                >
                  <th scope="row">{{ index + 1 }}</th>
                  <td>
                    <img
                      :src="'/assets/images/' + keranjang.products.gambar"
                      class="img-fluid shadow"
                      width="100"
                    />
                  </td>
                  <td>{{ keranjang.products.nama }}</td>
                  <td>
                    {{ keranjang.keterangan ? keranjang.keterangan : "-" }}
                  </td>
                  <td>{{ keranjang.jumlah_pesanan }}</td>
                  <td align="right">Rp {{ keranjang.products.harga }}</td>
                  <td align="right">
                    <strong
                      >Rp
                      {{ keranjang.products.harga * keranjang.jumlah_pesanan }}
                    </strong>
                  </td>
                  <td align="center" class="text-danger">
                    <b-icon-trash @click="hapusKeranjang(keranjang.id)" />
                  </td>
                </tr>
                <td colspan="6" align="right">
                  <strong>Total Harga :</strong>
                </td>
                <td align="right">
                  <strong>Rp {{ totalHarga }}</strong>
                </td>
                <td></td>
              </tbody>
            </table>
          </div>
        </div>
      </div>
      <!-- Form Checkout -->
      <div class="row justify-content-end">
        <div class="col-md-4">
          <form class="mt-4" v-on:submit.prevent>
            <div class="form-group">
              <label class="label-detail" for="nama">Nama :</label>
              <input
                type="text"
                class="form-control"
                v-model="pesan.nama"
                required
              />
            </div>
            <div class="form-group">
              <label class="label-detail" for="noMeja">Nomor Meja :</label>
              <input
                type="text"
                class="form-control"
                v-model="pesan.noMeja"
                required
              />
            </div>
            <button
              type="submit"
              class="btn btn-success float-right"
              @click="checkout"
            >
              <b-icon-cart></b-icon-cart> Checkout
            </button>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Navbar from "@/components/Navbar.vue";
import axios from "axios";
export default {
  name: "Keranjang",
  components: {
    Navbar,
  },
  data() {
    return {
      keranjangs: [],
      pesan: {},
    };
  },
  methods: {
    setKeranjangs(data) {
      this.keranjangs = data;
    },
    hapusKeranjang(id) {
      axios
        .delete("http://localhost:3000/keranjangs/" + id)
        .then(() => {
          this.$toast.error("Sukses Delete Keranjang", {
            type: "error",
            position: "top-right",
            duration: 3000,
            dismisable: true,
          });
          axios
            .get("http://localhost:3000/keranjangs")
            .then((response) =>
              // handle success
              this.setKeranjangs(response.data)
            )
            .catch((error) =>
              // handle error
              console.log("Gagal : ", error)
            );
        })
        .catch((error) => {
          console.log("Gagal : " + error);
        });
    },
    checkout() {
      if (this.pesan.nama && this.pesan.noMeja) {
        this.pesan.keranjangs = this.keranjangs;
        axios
          .post("http://localhost:3000/pesanans", this.pesan)
          .then(() => {
            this.keranjangs.map(function (item) {
              return axios
                .delete("http://localhost:3000/keranjangs/" + item.id)
                .catch((error) => {
                  console.log("Gagal : " + error);
                });
            });
            this.$router.push({ path: "/pesanan-sukses" });
            this.$toast.success("Orderan Berhasil Dipesan", {
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
        this.$toast.error("Nama dan No Meja Harus Diisi", {
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
      .get("http://localhost:3000/keranjangs")
      .then((response) =>
        // handle success
        this.setKeranjangs(response.data)
      )
      .catch((error) =>
        // handle error
        console.log("Gagal : ", error)
      );
  },
  computed: {
    totalHarga() {
      return this.keranjangs.reduce(function (items, data) {
        return items + data.products.harga * data.jumlah_pesanan;
      }, 0);
    },
  },
};
</script>

<style>
</style>