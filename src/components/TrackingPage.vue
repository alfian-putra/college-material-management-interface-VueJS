<template>
  <div class="tracking">
    <div class="trackingTitle">
      <h1>{{ msg }}</h1>
    </div>
    <div class="trackingHeader">
      <button @click="toggleFormTracking()" id="buttonPesan">Pesan</button><br><br>
      <input type="text" 
              v-model="searchDO" 
              id="inputDO" 
              @keyup.esc="clearDO"
              @keyup.enter="searchTracking(searchDO)"
              data-tooltip="Klik Esc untuk hapus, Enter untuk cari"></input>
      
      <button @click="searchTracking(searchDO)" id="buttonCari">Cari</button><br>
      <br>
    </div>
    <div class="trackingData" v-if="searchDO!=''">
        <div v-if="showData&&isExist(searchDO)">
          <div id="DO">{{searchDO}}</div>
          <table>
            <tbody>
              <tr  v-for="(value, key) in dataTracking[searchDO]">
                <td id="tdKey">{{key}}</td>
                <td id="tdVal" v-if="key!='perjalanan'">
                  <div v-if="key=='total'">
                    {{printRupiah(value)}}
                  </div>
                  <div v-else>
                    {{value}}
                  </div>
                </td>
              </tr>
            </tbody>
          </table>

          <ul>
            <li class="perjalanan" v-for="(perjalanan,index) in dataTracking[searchDO]['perjalanan']">
              <div v-if="index!=dataTracking[searchDO]['perjalanan'].length-1" class="liElement">
                {{perjalanan['waktu']}}<br>{{perjalanan['keterangan']}}<br></br>
              </div>
              <div v-else-if="index==dataTracking[searchDO]['perjalanan'].length-1" class="liLastElement">
                {{perjalanan['waktu']}}<br>{{perjalanan['keterangan']}}<br></br>
              </div>
            </li>
            <li>
              <div>
                <input v-model="formAddTrackingCheckpoint" id="inputTrackingCheckPoint"></input>
                <button @click.prevent="addTrackingCheckpoint()">+</button>
              </div>
            </li>
          </ul>

        </div>  
        <div v-if="showData&& !isExist(searchDO)">
          <div id="invalidData">Data tidak ditemukan !</div>
        </div>
      </div>
    </div>
    <div id="formContainer" v-if="openForm" >
      <form id="form">
        <h2>Pesan</h2>
        <div id="formMetric"><label for="nim">NIM :</label></div>
        <input type="text" v-model="formAdd.nim" id="nim" name="nim"><br><br>

        <div id="formMetric"> <label for="nama">Nama :</label></div>
        <input type="text" v-model="formAdd.nama" id="nama" name="nama"><br><br>

        <div id="formMetric"> Ekspedisi :</div>
        <select v-model="formAdd.ekspedisi" >
          <option  v-for="ekspedisi in pengirimanList" :key="ekspedisi">{{ ekspedisi.kode }}</option>
        </select><br> <br> 

        <div id="formMetric"> Paket Bahan Ajar :</div>
        <select v-model="formAdd.paket" >
          <option  v-for="p in paket">{{ p.kode }}</option>
        </select><br><br> 
        <div v-if="'paket' in formAdd">
          <table id="tablePaket" >
            <tbody>
              <tr  v-for="(v,k) in paket[idPaket(formAdd.paket)]">
                <td id="tablePaketMetric" v-if="k!='harga'">{{k}}</td>
                <td id="tablePaketData"v-if="k!='harga'">
                  <div v-if="k=='isi'" v-for="pkt in v">
                    {{pkt}}<br>
                  </div>
                  <div v-else>
                    {{v}}
                  </div>
                </td>
              </tr>
            </tbody>
          </table><br> <br> 

          <div id="formMetric"> Total Harga :</div>
          <div id="formHarga" v-if="'paket' in formAdd">{{ printRupiah(paket[idPaket(formAdd.paket)]['harga']) }}</div>
        </div><br>
      
        <div id="formMetric"> <label for="tanggalKirim">Tanggal Kirim :</label></div>
        <input type="date" v-model="formAdd.tanggalKirim" id="tanggalKirim" name="tanggalKirim" @keyup.enter="submitAddedTracking"><br><br> 
        

        <button type="submit" @click="submitAddedTracking">Submit</button>

        <button type="button" id="buttonCancel" @click="toggleFormTracking">Batal</button>
      </form>
  </div>
</template>

<script>
import { ref }from "vue";
var tooltip = require('tooltip');
tooltip()

export default {
  name: 'TrackingPage',
  setup(prop){
    const searchDO = ref("");
    const dataTracking = ref(prop.tracking);
    const resultDO = ref({});
    const formAdd = ref({});
    const openForm = ref(false);
    const showData = ref(false);

    const formAddTrackingCheckpoint = ref('')

    return{
      searchDO,
      dataTracking,
      resultDO,
      formAdd,
      openForm,
      showData,
      formAddTrackingCheckpoint
    }
  },
  props: {
    msg: String,
    pengirimanList: Array,
    paket: Array,
    tracking: Object
  },
  methods: {
    addTrackingCheckpoint(){
      function getFormattedDate() {
        const now = new Date()
        return now.toISOString().split('T')[0]
      }
      function getFormattedTime() {
        const now = new Date();
        const hours = now.getHours().toString().padStart(2, '0');
        const minutes = now.getMinutes().toString().padStart(2, '0');
        const seconds = now.getSeconds().toString().padStart(2, '0');
        return `${hours}:${minutes}:${seconds}`;
      }

      this.dataTracking[this.searchDO]["perjalanan"].push(
        {
          waktu : getFormattedDate()+" "+getFormattedTime(),
          keterangan : this.formAddTrackingCheckpoint
        }
      )
    },
    clearDO(){
      this.searchDO = '';
    },
    searchTracking(data){
      this.searchDO = data

      this.showData = !this.showData
    },
    isExist(data){
      return data in this.dataTracking
    },
    printRupiah(data){
      return new Intl.NumberFormat("id-ID", {
        style: "currency",
        currency: "IDR"
      }).format(data);
    },
    generateDO(){
      let tahun = new Date().getFullYear()
      let _length = Object.keys(this.dataTracking).length +1
      let id = _length.toString().padStart(4,'0')
      return "DO"+tahun+"-"+id
    },
    toggleFormTracking(){
      this.openForm = !this.openForm
    },
    submitAddedTracking(){
      
      this.formAdd['harga'] = this.paket[this.idPaket(this.formAdd['paket'])]['harga']
      this.formAdd['status'] = 'menunggu konfirmas'

      function getFormattedTime() {
        const now = new Date();
        const hours = now.getHours().toString().padStart(2, '0');
        const minutes = now.getMinutes().toString().padStart(2, '0');
        const seconds = now.getSeconds().toString().padStart(2, '0');
        return `${hours}:${minutes}:${seconds}`;
      }
      this.formAdd['perjalanan'] = [
      { waktu: this.formAdd.tanggalKirim+" "+getFormattedTime() , keterangan: "Pesanan dibuat." }
      ]

      this.dataTracking[this.generateDO()] = this.formAdd
      this.formAdd = {}
      console.log(this.dataTracking)

      this.toggleFormTracking()
    },
    idPaket(kode){
      let id=0;

      for(let i=0;i<this.paket.length;i++){
        if(this.paket[i]['kode']==kode){
          break
        }

        id++;
      }
      return id

    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.tracking{
    display: grid;
    grid-template-columns: repeat(10, 1fr);
    grid-template-rows: repeat(6, 1fr);
}
.trackingTitle{
    grid-column: span 8 / span 8;
    grid-column-start: 2;
    grid-row-start: 1;
    height: 100px;

    text-align: center;

}
.trackingHeader{
    grid-column: span 8 / span 8;
    grid-column-start: 2;
    grid-row-start: 2;

    box-shadow: rgba(100, 100, 111, 0.2) 0px 4px 10px 0px;
    padding: 20px;
    height: 100px;
    text-align: left;

    border-radius: 15px;
}
.trackingData{
    grid-column: span 8 / span 8;
    grid-row: span 3 / span 3;
    grid-column-start: 2;
    grid-row-start: 3;

    box-shadow: rgba(100, 100, 111, 0.2) 0px 4px 10px 0px;
    padding: 20px;

    border-radius: 15px;
}
form>h2{
  text-align: center;
}
#formMetric{
  font-weight: bold;
}
#formContainer{
  box-shadow: rgba(100, 100, 111, 0.2) 0px 4px 10px 0px;
  background-color: white;
  border-radius: 20px;
  width: 400px;
  height: auto;
  padding: 50px;

  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
}

#tablePaket, #tablePaketMetric, #tablePaketData{
  border: 1px solid #19183B;
}
#tablePaket{
  border-collapse: collapse;
}
#tablePaketMetric{
  font-weight: bold ;
  width: 70px;
  padding-left: 10px;
}
#tablePaketData{
  width: 150px;
  padding: 5px 0px 5px 10px;
}
h3 {
  margin: 40px 0 0;
}
li{
  text-align: left;
  padding-bottom: 10px;
}
#form{
  text-align: left;
}
.perjalanan::marker{
  color: grey;
}
.liLastElement{
  color:#42b983;
}
.perjalanan:has(.liLastElement)::marker{
  color:#42b983;
}
.liElement{
  color: grey;
}
a {
  color: #42b983;
}
button{
  background-color: #5682B1;
  color: white;
  height: 30px;
  width: 120px;
  border-radius: 10px;
  margin-right: 10px;
}
button:hover{
  background-color: #19183B;
  color: white;
  cursor: pointer;
}
#tdKey{
  width: 150px;
  text-align: left;
  font-weight: bold;
  line-height: 100%;
  max-height: 500px;
}
#tdVal{
  width: 300px;
  text-align: left;
}
#DO{
  text-align: center;
  font-weight: bold;
}
#buttonPesan{
  width: 100%;
}
#buttonCari{
  background-color: #FFD93D;
  color: #19183B;
  border: none;
  height: 30px;
  
}
#buttonCari:hover{
  background-color: gray;
  color: white;
  border: none;
  height: 30px;
}
#inputDO, form>input[type="text"]{
  border: 1px solid #778873;
  border-radius: 7px;
  margin-right: 10px;


  height: 20px;
}
select{
  background-color: #b6bcc4;
  color: #19183B;
  height: 30px;
  width: 120px;
  border-radius: 10px;
}
#buttonCancel{
  background-color: white;
  border-color: #5682B1;
  color: #5682B1;
  height: 30px;
  width: 120px;
  border-radius: 10px;
}
#invalidData{
  color: #E62727;
  font-size: 10pt;
}
</style>
