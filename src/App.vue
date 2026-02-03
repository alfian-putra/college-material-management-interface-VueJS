<template>
  <div id="header">
    <a href="#" @click="setPage('stok')">Stok</a>
    <a href="#" @click="setPage('tracking')">Tracking</a>
  </div>

  <div id="page" v-if="page=='stok'">
    <StockPage msg="Stock Bahan Ajar" :data="stok" :upbjjList="upbjjList" :kategoriList="kategoriList"/>
  </div>
  <div id="page" v-if="page=='tracking'">
    <TrackingPage msg="Stock Bahan Ajar" :pengirimanList="pengirimanList" :paket="paket" :tracking="tracking"/>
  </div>
  </template>

<script>
import TrackingPage from './components/TrackingPage.vue'
import StockPage from './components/StockPage.vue'

export default {
  name: 'App',
  components: {
    TrackingPage,
    StockPage
  },
  data(){
    return{
      upbjjList: ["Jakarta", "Surabaya", "Makassar", "Padang", "Denpasar"],
      kategoriList: ["MK Wajib", "MK Pilihan", "Praktikum", "Problem-Based"],
      pengirimanList: [
        { kode: "REG", nama: "Reguler (3-5 hari)" },
        { kode: "EXP", nama: "Ekspres (1-2 hari)" }
      ],
      paket: [
        { kode: "PAKET-UT-001", nama: "PAKET IPS Dasar", isi: ["EKMA4116","EKMA4115"], harga: 120000 },
        { kode: "PAKET-UT-002", nama: "PAKET IPA Dasar", isi: ["BIOL4201","FISIP4001"], harga: 140000 }
      ],
      stok: [
        {
          kode: "EKMA4116",
          judul: "Pengantar Manajemen",
          kategori: "MK Wajib",
          upbjj: "Jakarta",
          lokasiRak: "R1-A3",
          harga: 65000,
          qty: 28,
          safety: 20,
          catatanHTML: "<em>Edisi 2024, cetak ulang</em>"
        },
        {
          kode: "EKMA4115",
          judul: "Pengantar Akuntansi",
          kategori: "MK Wajib",
          upbjj: "Jakarta",
          lokasiRak: "R1-A4",
          harga: 60000,
          qty: 7,
          safety: 15,
          catatanHTML: "<strong>Cover baru</strong>"
        },
        {
          kode: "BIOL4201",
          judul: "Biologi Umum (Praktikum)",
          kategori: "Praktikum",
          upbjj: "Surabaya",
          lokasiRak: "R3-B2",
          harga: 80000,
          qty: 1,
          safety: 10,
          catatanHTML: "Butuh <u>pendingin</u> untuk kit basah"
        },
        {
          kode: "FISIP4001",
          judul: "Dasar-Dasar Sosiologi",
          kategori: "MK Pilihan",
          upbjj: "Makassar",
          lokasiRak: "R2-C1",
          harga: 55000,
          qty: 2,
          safety: 8,
          catatanHTML: "Stok <i>menipis</i>, prioritaskan reorder"
        }
      ],
      // Simulasi status DO (opsional fitur Tracking DO)
      tracking: {
        "DO2025-0001": {
          nim: "123456789",
          nama: "Rina Wulandari",
          status: "Dalam Perjalanan",
          ekspedisi: "JNE",
          tanggalKirim: "2025-08-25",
          paket: "PAKET-UT-001",
          total: 120000,
          perjalanan: [
            { waktu: "2025-08-25 10:12:20", keterangan: "Penerimaan di Loket: TANGSEL" },
            { waktu: "2025-08-25 14:07:56", keterangan: "Tiba di Hub: JAKSEL" },
            { waktu: "2025-08-26 08:44:01", keterangan: "Diteruskan ke Kantor Tujuan" }
          ]
        }
      },
      page: "tracking"
    }
  },
  methods: {
    setPage(data){
      this.page = data
    }
  }
}
</script>

<style>
html, body{
  margin:0;
  padding: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#header{
  height: 60px;
  background-color: #2c3e50;
  text-align: left;
  margin-bottom: 20px;
}
#header>a{
  height: 40px;
  padding: 20px;
  width: 100%;
  max-width: 60px;

  text-align: center;
  font-weight: bold;
  color: antiquewhite;

  text-decoration: none;
  line-height: 60px;
}
#header>a:hover{
  background-color: #FFD93D;
  color: #2c3e50;
}
</style>
