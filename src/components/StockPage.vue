<template>
  <div class="StockPage_cls">
    <h1 class="stockPage_title">{{ msg }}</h1>
    <div class="stockPage_header">
      <div class="stockPage_header_sort">
        <div class="stockPage_header_sort_by">
          <b>Sort By :</b><br> 
          <div id="radio_parent">
            <div id="radio_item">
              <input type="radio" name="sort_by" id="judul" value="judul" v-model="sortBy" />
              <label for="judul">Judul</label>
            </div>
            
            <div id="radio_item">
              <input type="radio" name="sort_by" id="harga" value="harga" v-model="sortBy" />
              <label for="harga">harga</label>
            </div>

            <div id="radio_item">
              <input type="radio" name="sort_by" id="stok" value="stok" v-model="sortBy" />
              <label for="stok">stok</label>
            </div>
          </div>
        </div>
        <div class="stockPage_header_sort_direction">
          <b>Sort Direction :</b><br> 
          <div id="radio_parent">
            <div id="radio_item">
              <input type="radio" name="sortDirection" id="asc" value="asc" v-model="sortDirection" />
              <label for="asc">ascending</label>
            </div>
            <div id="radio_item">
              <input type="radio" name="sortDirection" id="desc" value="desc" v-model="sortDirection" />
              <label for="desc">descending</label>
            </div>
          </div>

        </div>
        <br> 



      </div>
      <div class="stockPage_header_filter">
          <div class="stockPage_header_filter_by">
            <b>Filter By :</b><br> 
            <select v-model="filterBy">
              <option disabled value="">Select Filter By</option>
              <option value="upbjj">UPBJJ</option>
              <option value="stok">Status Stok</option>
            </select>
          </div>
      </div>
      <div class="stockPage_header_filter_1" v-if="filterBy!=''">
        <div v-if="filterBy=='upbjj'">
            <b>UPBJJ</b><br> 
            <select v-model="filterUPBJJ">
              <option  v-for="upbjj in upbjjList" :key="upbjj">{{ upbjj }}</option>
            </select><br> 
            <b>Kategori Matkul</b><br> 
            <select v-model="filterMatkul">
              <option  v-for="kategori in kategoriList" :key="kategori">{{ kategori }}</option>
            </select>
          </div>
          <div v-if="filterBy=='stok'">
            <br> 
            <b>Ketersediaan</b><br> 
            <select v-model="filterStok">
              <option>tersedia</option>
              <option>menipis</option>
              <option>habis</option>
            </select>
          </div>
      </div>
      <div class="stockPage_header_reset">
        <button class="resetButton" @click="resetSortFilter">reset</button>
      </div>

          

    </div>


    <div class="stockPage_data">
        <table>
          <thead>
              <tr>
                <th>Kode</th>
                <th>Judul</th>
                <th>Kategori</th>
                <th>UPBJJ</th>
                <th>Lokasi</th>
                <th>Harga</th>
                <th>Quantity</th>
                <th>Safety</th>
                <th>Catatan</th>
                <th>Ubah</th>
                <th>Hapus</th>
              </tr>
          </thead>
          <tbody>
              <tr v-for="bmp in processedData" :key="bmp" >
                <td v-if="filter(bmp)">{{ bmp.kode }}</td>
                <td v-if="filter(bmp)">{{ bmp.judul }}</td>
                <td v-if="filter(bmp)">{{ bmp.kategori }}</td>
                <td v-if="filter(bmp)">{{ bmp.upbjj }}</td>
                <td v-if="filter(bmp)">{{ bmp.lokasiRak }}</td>
                <td v-if="filter(bmp)">{{ printRupiah(bmp.harga) }}</td>
                <td v-if="filter(bmp)">{{ bmp.qty }}</td>
                <td v-if="filter(bmp)">{{ bmp.safety }}</td>
                <td v-if="filter(bmp)"><div  v-html="bmp.catatanHTML"></div></td>
                <td v-if="filter(bmp)">
                  <button id="buttonUbah" @click="editForm(bmp)">Ubah</button>
                </td>
                <td v-if="filter(bmp)">
                  <button id="buttonHapus" @click="hapusData(bmp)">Hapus</button>
                </td>
              </tr>
          </tbody>
        </table>
        
        <div v-if="showEditForm" id="popupFormContainer">
          <form id="popupForm">
             
            <h2>Ubah Stok</h2>
            <label for="kode">Kode :</label><br>
            <input type="text" v-model="formData.kode" id="kode" name="kode" value={{formData.kode}}><br>

            <label for="judul">Judul :</label><br>
            <input type="text" v-model="formData.judul" id="judul" name="judul"><br>

            Kategori :<br> 
            <select v-model="formData.kategori" >
              <option  v-for="kategori in kategoriList" :key="kategori">{{ kategori }}</option>
            </select><br> 

            UPBJJ :<br> 
            <select v-model="formData.upbjj" >
              <option  v-for="upbjj in upbjjList" :key="upbjj">{{ upbjj }}</option>
            </select><br> 

            <label for="lokasiRak">Lokasi Rak :</label><br> 
            <input v-model="formData.lokasiRak" type="text" id="lokasiRak" name="lokasiRak"><br> 
            <div v-if="formData.lokasiRak!=''&&!validateLokasiRak(formData.lokasiRak)" id="invalidData">
              Data tidak sesuai ! format : [huruf-kapital][angka]-[huruf-kapital][angka] <br>
              Contoh : R1-A3
            </div><br> 

            <label for="harga">Harga :</label><br> 
            <input type="text" v-model="formData.harga" id="harga" name="harga"><br> 
            <div v-if="!validateInt(formData.harga)" id="invalidData">
              Data tidak sesuai !
            </div><br> 

            <label for="qty">Quantity :</label><br> 
            <input type="text" v-model="formData.qty" id="qty" name="qty"><br> 
            <div v-if="!validateInt(formData.qty)" id="invalidData">
              Data tidak sesuai !
            </div><br> 

            <label for="safety">Safety (Alert stok menipis) :</label><br> 
            <input type="text" v-model="formData.safety" id="safety" name="safety"><br> 
            <div v-if="!validateInt(formData.safety)" id="invalidData">
              Data tidak sesuai !
            </div><br> 

            <div id="inputCatatan" data-tooltip="catatan menggunakan format HTML">
              <label for="catatanHTML" v-on:keyup.enter="submitEditedData">catatan (HTML) :</label><br> 
              <input type="text" v-model="formData.catatanHTML" id="catatanHTML" name="catatanHTML"><br> 
            </div>

            <br>
            <button type="submit" @click.prevent="submitEditedData">Submit</button>
            <button type="button" id="buttonCancel" @click="toggleFormEdit">Batal</button>
          </form>
        </div>

        <button id="buttonTambah" @click="toggleFormAdd">Tambah</button>
        <div v-if="showAddForm" id="popupFormContainer">
          <form id="poppupForm"> 
            <h2>Tambah Stok</h2>
            <label for="kode">Kode :</label><br>
            <input type="text" v-model="formData.kode" id="kode" name="kode"><br>

            <label for="judul">Judul :</label><br>
            <input type="text" v-model="formData.judul" id="judul" name="judul"><br>

            Kategori :<br> 
            <select v-model="formData.kategori" >
              <option  v-for="kategori in kategoriList" :key="kategori">{{ kategori }}</option>
            </select><br> 

            UPBJJ :<br> 
            <select v-model="formData.upbjj" >
              <option  v-for="upbjj in upbjjList" :key="upbjj">{{ upbjj }}</option>
            </select><br> 

            <label for="lokasiRak">Lokasi Rak :</label><br> 
            <input v-model="formData.lokasiRak" type="text" id="lokasiRak" name="lokasiRak"><br> 
            <div v-if="formData.lokasiRak!=''&&!validateLokasiRak(formData.lokasiRak)" id="invalidData">
              Data tidak sesuai ! format : [huruf-kapital][angka]-[huruf-kapital][angka] <br>
              Contoh : R1-A3
            </div><br> 

            <label for="harga">Harga :</label><br> 
            <input type="text" v-model="formData.harga" id="harga" name="harga"><br> 
            <div v-if="!validateInt(formData.harga)" id="invalidData">
              Data tidak sesuai !
            </div><br> 

            <label for="qty">Quantity :</label><br> 
            <input type="text" v-model="formData.qty" id="qty" name="qty"><br> 
            <div v-if="!validateInt(formData.qty)" id="invalidData">
              Data tidak sesuai !
            </div><br> 

            <label for="safety">Safety (Alert stok menipis) :</label><br> 
            <input type="text" v-model="formData.safety" id="safety" name="safety"><br> 
            <div v-if="!validateInt(formData.safety)" id="invalidData">
              Data tidak sesuai !
            </div><br> 
            <div id="inputCatatan" data-tooltip="catatan menggunakan format HTML">
              <label for="catatanHTML" v-on:keyup.enter="submitAddedData">catatan (HTML) :</label><br> 
              <input type="text" v-model="formData.catatanHTML" id="catatanHTML" name="catatanHTML"><br> 
            </div>

            <br>
            <button type="submit" @click="submitAddedData">Submit</button>

            <button type="button" id="buttonCancel" @click="toggleFormAdd">Batal</button>
          </form>
        </div>

    </div>
  </div>
</template>

<script>
import { ref }from "vue";
var tooltip = require('tooltip');
tooltip()

export default {
  name: 'StockPage',
  setup(props){
    const sortBy= ref("judul");
    const sortDirection = ref("asc");
    const filterBy= ref("");
    const filterStok = ref("");
    const filterUPBJJ = ref("");
    const filterMatkul = ref("");
    const processedData= ref(props.data);

    const formData = ref({});
    const showAddForm = ref(false);

    const showEditForm = ref(false);
    const idEditData = ref(-1);

    const idHapustData = ref();

    return { 
      sortBy,
      filterBy,
      sortDirection,
      processedData,
      filterStok,
      filterUPBJJ,
      filterMatkul,
      formData,
      showAddForm,
      showEditForm,
      idEditData,
      idHapustData
    }
  },
  props: {
    msg: String,
    data: Array,
    upbjjList: Array,
    kategoriList: Array
  },
  // computed: {
  //   processedData : function(){
  //     return this.data
  //   }
  // },
  methods : {
    printRupiah(data){
      return new Intl.NumberFormat("id-ID", {
        style: "currency",
        currency: "IDR"
      }).format(data);
    },
    sortByJudul(){
        if(this.sortDirection=="desc"){
          this.processedData.sort((a,b) => {
            if (a.judul > b.judul) return -1;
            if (a.judul < b.judul) return 1;
            // Both idential, return 0
            return 0;
          })
        }else{
          this.processedData.sort((a,b) => {
            if (a.judul < b.judul) return -1;
            if (a.judul > b.judul) return 1;
            // Both idential, return 0
            return 0;
          }
          )
        }
    },
    sortByHarga(){
      if(this.sortDirection=="desc"){
        this.processedData.sort((a,b) => b.harga - a.harga)
      }else{
        this.processedData.sort((a,b) => a.harga - b.harga)
      }
    },
    sortByStok(){
      if(this.sortDirection=="desc"){
        this.processedData.sort((a,b) => b.qty - a.qty)
      }else{
        this.processedData.sort((a,b) => a.qty - b.qty)
      }
    },
    filterByUPBJJ(data){
      console.log(data)
      return data.upbjj==this.filterUPBJJ
    },
    filterByMatkul(data){
      return data.kategori===this.filterMatkul
    },
    filterByStok(data){
      switch(this.filterStok){
        case "tersedia":
          return  data.qty>data.safety
        case "menipis":
          return data.qty<data.safety && data.qty>0
        case "habis":
          return data.qty==0
      }
    },
    filter(data){
        if(this.filterBy=="upbjj"&&this.filterUPBJJ!=""){
          if(this.filterByMatkul && this.filterMatkul!=""){
            return this.filterByUPBJJ(data) && this.filterByMatkul(data)
          }
          return this.filterByUPBJJ(data);
        }else if(this.filterBy=="stok" && this.filterStok!=""){
          return this.filterByStok(data)
        }
      return true
    },
    sort(){
      switch(this.sortBy){
        case "judul":
          this.sortByJudul();
          break;
        case "harga":
          this.sortByHarga();
          break;
        case "stok" :
          this.sortByStok();
          break;
      }
    },
    resetSortFilter(){
      this.sortBy="judul";
      this.sortDirection="asc";
      this.filterBy= "";
      this.filterStok = "";
      this.filterUPBJJ = "";
      this.filterMatkul = "";
    },
    validateInt(val){
      return !isNaN(val);
    },
    validateLokasiRak(val){
      let pattern = /[A-Z]+[1-9]+-[A-Z]+[1-9]/
      return pattern.test(val)
    },
    toggleFormAdd(){
        this.formData.kode = ""
        this.formData.judul = ""
        this.formData.kategori = ""
        this.formData.upbjj = ""
        this.formData.lokasiRak = ""
        this.formData.harga = ""
        this.formData.qty = ""
        this.formData.safety = ""
        this.formData.catatanHTML = ""

      this.showAddForm = ! this.showAddForm
    },
    toggleFormEdit(){
      this.showEditForm = ! this.showEditForm
    },
    editForm(data){
      this.formData.kode = data.kode
      this.formData.judul = data.judul
      this.formData.kategori = data.kategori
      this.formData.upbjj = data.upbjj
      this.formData.lokasiRak = data.lokasiRak
      this.formData.harga = data.harga
      this.formData.qty = data.qty
      this.formData.safety = data.safety
      this.formData.catatanHTML = data.catatanHTML

      for(let i=0; i<this.processedData.length; i++){
        if(this.processedData[i]["kode"]==data.kode){
          this.idEditData = i;
          break;
        }
      }

      this.toggleFormEdit()
    },
    submitEditedData(){
      this.processedData[this.idEditData] = this.formData;
      this.toggleFormEdit()
    },
    submitAddedData(){
      this.processedData.push(this.formData);
      this.showAddForm = ! this.showAddForm
    },
    hapusData(data){
      for(let i=0; i<this.processedData.length; i++){
        if(this.processedData[i]["kode"]==data.kode){
          this.idHapustData = i;
          break;
        }
      }

      this.processedData.splice(this.idHapustData,1);
      //location.reload();
    }
  },
  watch:{
    filterBy(newVal, oldVal){
        this.filter()
        console.log("filter")
    },
    filterByStok(newVal, oldVal){
        this.filter()
        console.log("filter")
    },
    sortBy(newVal, oldVal){
        this.sort()
        console.log("sort")
      },
    sortDirection(newVal, oldVal){
        this.sort()
        console.log("sort")
      }
    },

    }



</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h2 {
  text-align: center;
}
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
b {
  width: 100%;
}
th, td {
  padding-left: 15px;
  padding-right: 15px;
  padding-bottom: 5px;
}
select{
  background-color: #5682B1;
  color: white;
  height: 30px;
  width: 120px;
  border-radius: 10px;
}
input[type="text"]{
  margin-bottom: 10px;
  border-radius: 5px;
  width: 70%;
}
button{
  background-color: #5682B1;
  color: white;
  height: 30px;
  width: 120px;
  border-radius: 10px;
}
button:hover{
  background-color: #19183B;
  color: white;
  cursor: pointer;
}
#buttonHapus{
  background-color: #d1514c;
  color: white;
  height: 30px;
  width: 120px;
  border-radius: 10px;
}
#buttonHapus:hover{
  background-color: #4e1b15;
  color: white;
  cursor: pointer;
}
.resetButton{
  background-color: #FFD93D;
  color: #19183B;
  border: none;
}
#buttonCancel{
  background-color: white;
  border-color: #5682B1;
  color: #5682B1;
  height: 30px;
  width: 120px;
  border-radius: 10px;
}
#radio_parent{
  display: flex;
  flex-direction: row;
}
#radio_item{
  padding-right: 1px;
  width: 30%;
}
#invalidData{
  color: #E62727;
  font-size: 10pt;
}
#popupFormContainer{
  padding: 50px;
  text-align: left;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}
.StockPage_cls{
    display: grid;
    grid-template-columns: repeat(10, 1fr);
    grid-template-rows: repeat(6, 1fr);
}
.stockPage_title{
    grid-column: span 8 / span 8;
    grid-column-start: 2;
    grid-row-start: 1;
    height: 100px;

    text-align: center;
}
.stockPage_header{
    grid-column: span 8 / span 8;
    grid-column-start: 2;
    grid-row-start: 2;

    box-shadow: rgba(100, 100, 111, 0.2) 0px 4px 10px 0px;
    padding: 20px;
    height: auto;
    text-align: left;

    border-radius: 15px;

    display: grid;
    grid-template-columns: repeat(10, 1fr);
    grid-template-rows: repeat(2, 1fr);
    gap: 8px;
}
.stockPage_header_sort{
  grid-column: span 2 / span 2;
  grid-row: span 2 / span 2;
}
.stockPage_header_filter{
  grid-column: span 1 / span 1;
    grid-row: span 2 / span 2;
    grid-column-start: 3;
}
.stockPage_header_filter_1{
    grid-column: span 2 / span 2;
    grid-row: span 2 / span 2;
    grid-column-start: 4;
}
.stockPage_header_sort > div{
  text-align: left;
}
.stockPage_header_filter > div{
  text-align: left;
}
.stockPage_header_reset{
  grid-column-start: 10;
}
.stockPage_data{
  grid-column: span 8 / span 8;
    grid-row: span 3 / span 3;
    grid-column-start: 2;
    grid-row-start: 3;

    box-shadow: rgba(100, 100, 111, 0.2) 0px 4px 10px 0px;
    padding: 50px;  display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;

    border-radius: 15px;
    width: auto;

    display: flex;
  justify-content: center;
  align-items: center;
}
.stockPage_data>table{
  width:100%;
}
.buttonTambah{
  width: 100%;
}
#popupFormContainer{
  background-color: white;
  width: 400px;
  height: 800px;
  border-radius: 25px;

  box-shadow: rgba(100, 100, 111, 0.2) 0px 7px 29px 0px;
}
</style>
