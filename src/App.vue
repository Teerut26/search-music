<template>
  <div class="container mt-3">
    <div class="card">
      <div class="card-body">
        <h3>ค้นหาเพลง</h3>
        <hr>
        <div class="form-group">
          <div class="input-group mb-3">
            <input @keyup.enter="searchMusic(form.keyWord)" @keyup="searchMusic(form.keyWord)" type="text"
              class="form-control" placeholder="Word" v-model="form.keyWord">
            <button type="submit" @click="searchMusic(form.keyWord)" class="btn btn-success">ค้นหา</button>
            <button type="submit" @click="resetForm()" class="btn btn-danger">ล้าง</button>
          </div>
          <div class="form-group">
            <div class="btn-group" role="group" aria-label="Basic example">
              <button type="button" :class="{active:limit === 20}" @click="this.limit = 20,searchMusic(form.keyWord)"
                class="btn btn-primary">20</button>
              <button type="button" :class="{active:limit === 10}" @click="this.limit = 10,searchMusic(form.keyWord)"
                class="btn btn-primary">10</button>
              <button type="button" :class="{active:limit === 5}" @click="this.limit = 5,searchMusic(form.keyWord)"
                class="btn btn-primary">5</button>
              <button type="button" :class="{active:limit === 1}" @click="this.limit = 1,searchMusic(form.keyWord)"
                class="btn btn-primary">1</button>
            </div>
          </div>
        </div>
        <hr>
        <div class="table-responsive" v-if="showTable">
          <table class="table table-bordered">
            <thead class="table-dark">
              <tr>
                <th>Cover</th>
                <th>Title</th>
                <th>Artist</th>
                <th>trackID</th>
                <th>CopyID</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="item in songType" :key="item">
                <td><img :src="item.artworkUrl100" alt="" srcset=""></td>
                <td>{{item.trackName}}</td>
                <td>{{item.artistName}}</td>
                <td><input class="form-control inputTrackid" :id="item.trackId" type="text" :value="item.trackId"><input class="form-control inputTrackid" :id="item.trackId+'_title'" type="text" :value="item.trackName+' - '+item.artistName"></td>
                <td><button class="btn btn-success btn-sm" @click="copyTextID(item.trackId)">CopyID</button> <button class="btn btn-success btn-sm" @click="copyTextTitle(item.trackId,item.trackName+' - '+item.artistName)">CopyTitle</button></td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  const axios = require('axios');
  const Swal = require('sweetalert2');
  export default {
    name: 'App',
    data() {
      return {
        form: {
          keyWord: "",
        },
        dataRes: [],
        showTable: false,
        limit: 20

      }
    },
    methods: {
      getFirst: function () {
        axios
          .get('https://itunes.apple.com/search?term=รักเดียว&limit=20')
          .then((response) => {
            this.discordData = response.data.members
            this.discordDataAll = response.data
          })
          .catch((error) => {
            console.error(error)
          });
      },
      searchMusic: function (word) {
        var url = 'https://itunes.apple.com/search?term=' + encodeURIComponent(word) + '&limit=' + this.limit;
        axios
          .get(url)
          .then((response) => {
            this.dataRes = response.data.results
            this.showTable = true
          })
          .catch((error) => {
            console.error(error)
          });
      },
      resetForm: function () {
        this.dataRes = []
      },
      copyTextID: function (id) {
        /* Get the text field */
        console.log(id);
        var copyText = document.getElementById(id);

        /* Select the text field */
        copyText.select();
        copyText.setSelectionRange(0, 99999); /*For mobile devices*/

        /* Copy the text inside the text field */
        document.execCommand("copy");
        Swal.fire({
          title: 'คัดลอกสำเร็ว',
          text: 'คักลอกข้อความของคุณแล้ว : ' + id,
          icon: 'success',
          confirmButtonText: 'OK!'
        })
      },
      copyTextTitle: function (id,title) {
        /* Get the text field */
        console.log(id);
        var copyText = document.getElementById(id+'_title');

        /* Select the text field */
        copyText.select();
        copyText.setSelectionRange(0, 99999); /*For mobile devices*/

        /* Copy the text inside the text field */
        document.execCommand("copy");
        Swal.fire({
          title: 'คัดลอกสำเร็ว',
          text: title,
          icon: 'success',
          confirmButtonText: 'OK!'
        })
      },
    },
    computed: {
      songType: function () {
        return this.dataRes.filter(item => {
          if (item.kind === 'song') {
            return true
          } else {
            return false
          }
        })
      }
    },
    created() {

    }
  }
</script>

<style>
  @import url('https://fonts.googleapis.com/css2?family=Kanit&display=swap');

  #app,
  body {
    font-family: 'Kanit', sans-serif;
  }

  .inputTrackid {
    width: 110px;
  }
</style>