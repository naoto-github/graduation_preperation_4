<template>
    <v-ons-page>
      <v-ons-toolbar>
        <div class="center">{{ title }}</div>
        <div class="right">
          <v-ons-toolbar-button>
            <v-ons-icon icon="ion-navicon, material: md-menu"></v-ons-icon>
          </v-ons-toolbar-button>
        </div>
      </v-ons-toolbar>

      <v-ons-list>
        <v-ons-list-item v-for="(object, $index) in object_list" :key="object" tappable>
          <label class="left">
            <v-ons-checkbox
              :input-id="'checkbox-' + $index"
              :value="object"
              v-model="checked_list"
            >
            </v-ons-checkbox>
          </label>
          <label class="center" :for="'checkbox-' + $index">
            {{ object }}
          </label>
        </v-ons-list-item>
        <v-ons-list-item>
          <v-ons-button @click="filter" modifier="large" style="margin: 6px 0">更新</v-ons-button>
        </v-ons-list-item>
      </v-ons-list>

      <div id="map" style="height:360px"></div>
    </v-ons-page>
</template>
<script>
  import kyouiku from "./js/kyouiku"
  import iryou from "./js/iryou"

  export default{
    data() {
      return {
        title: "地図アプリ",
        map: null,
        lat: 35.130621, 
        lng: 137.037568,
        zoom: 14,
        kyouiku_group: null,
        iryou_group: null,
        object_list: ["教育機関", "医療機関"],
        checked_list: ["教育機関", "医療機関"]
      };
    },
    methods: {
      filter(){

        // マーカーを削除
        this.kyouiku_group.removeFrom(this.map);
        this.iryou_group.removeFrom(this.map);

        //チェックされているマーカーを追加
        for(let item of this.checked_list){
          if(item == "教育機関"){
            this.kyouiku_group.addTo(this.map);
          }
          else if(item == "医療機関"){
            this.iryou_group.addTo(this.map);
          }
        }
      }
    },
    mounted: function(){

      this.map = L.map("map");
      this.map.setView([this.lat, this.lng], this.zoom);

      // オープンストリートマップ
      L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', 
      {
        attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
      }).addTo(this.map);

      // 教育機関のマーカー
      this.kyouiku_group = L.featureGroup()
      for(let data of kyouiku.kyouiku_list){
        let lat = Number(data["緯度"]); // 緯度
        let lng = Number(data["経度"]); // 経度
        if(!isNaN(lat) || !isNaN(lng)){
          let marker = L.circleMarker([lat, lng], {color: "#ff0000"}); // マーカーは赤色
          marker.bindPopup(data["名称"]) // ポップアップ表示
          this.kyouiku_group.addLayer(marker);
        }
      }
      this.kyouiku_group.addTo(this.map);

      // 医療機関のマーカー
      this.iryou_group = L.featureGroup()
      for(let data of iryou.iryou_list){
        let lat = Number(data["緯度"]); // 緯度
        let lng = Number(data["経度"]); // 経度
        if(!isNaN(lat) || !isNaN(lng)){
          let marker = L.circleMarker([lat, lng], {color: "#00ff00"}); // マーカーは緑色
          marker.bindPopup(data["施設名称"]) // ポップアップ表示
          this.iryou_group.addLayer(marker);
        }
      }
      this.iryou_group.addTo(this.map);


    }
  }
</script>
