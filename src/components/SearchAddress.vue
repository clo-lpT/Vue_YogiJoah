<template>
  <div id="SearchAddress">
    <div class="main_class">
      <div class="find_juso_map">
        <input type="button" v-on:click="load_juso" value="주소 검색"><br>
        <div id="map" style="width:300px;height:300px;margin-top:10px;"></div> 

        <input type="text" id="memo" v-model="memo" placeholder="메모">
        <input type="button" v-on:click="add_place" value="장소 추가"><br>
      </div>
      <div class="set_juso_list">
        <h4>요기조아</h4>
        <div class="joah" v-for="item in joah_list" v-bind:key="item.id">
          <span id="place">위치: {{item.place}}</span>
          <span id="memo">메모: {{item.memo}}</span>
          <button v-on:click="delete_place(item)">삭제</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'SearchAddress',
  data() {
    return {
      center: new window.kakao.maps.LatLng(33.450701, 126.570667),
      geocoder: new window.kakao.maps.services.Geocoder(),
      joah_add: '',
      joah_list: []
    }
  },
  props: {
  },
  mounted () {
  var container = document.getElementById('map');
      var options = {
        center: this.center,
        level: 3
      };
  new window.daum.maps.Map(container, options);
  },
  methods: {
    load_juso() {
      new window.daum.Postcode({
        oncomplete: (data) => {
          var addr = data.address; // 최종 주소 변수
          console.log(addr,'주소')
          this.joah_add = addr;
          // // 주소로 상세 정보를 검색
          new window.kakao.maps.services.Geocoder().addressSearch(data.address, function(results, status) {
            // 정상적으로 검색이 완료됐으면
            if (status === window.daum.maps.services.Status.OK) {
              var result = results[0]; //첫번째 결과의 값을 활용
              // 해당 주소에 대한 좌표를 받아서
              var coords = new window.daum.maps.LatLng(result.y, result.x);
              // 지도를 보여준다.
              var container = document.getElementById('map');
              var map = new window.daum.maps.Map(container, {center: coords,level: 3});
              container.style.display = "block";
              map.relayout();
              map.setCenter(coords);
              new window.daum.maps.Marker({position: coords, map: map}).setPosition(coords);
            }
          });
        }
      }).open();
    },
    add_place() {
      const placeList = []
      if(this.joah_list.length == 0){
        placeList.id = 1
      } else {
        placeList.id = this.joah_list[this.joah_list.length-1].id +1
      }
      placeList.place = this.joah_add
      placeList.memo = this.memo
      placeList.toggle = false
      this.joah_list.push(...[placeList])
      console.log(placeList)
      this.memo = ''
    },
    delete_place(item) {
      console.log(item)
      const arr = this.joah_list.filter(x => x.id!==item.id)
      this.joah_list = arr
    }
  }
}
</script>

<style scoped>
#SearchAddress {
  display: flex;
}

#SearchAddress .main_class {
  display: flex;
  width: 100%;
  justify-content: space-evenly;
  flex-wrap: wrap;
}

#SearchAddress .main_class .find_juso_map .juso_input {
  display: flex;
  border-bottom: 2px solid gray;
}

#SearchAddress .main_class .set_juso_list {
  width: 500px;
}

#SearchAddress .main_class .set_juso_list .joah {
  display: flex;
  justify-content: space-between;
}

#SearchAddress .main_class .set_juso_list .joah #place {
  width: 250px;
  text-overflow: ellipsis;
  overflow: hidden;
  white-space: nowrap;
}

#SearchAddress .main_class .set_juso_list .joah #memo {
  width: 130px;
  text-overflow: ellipsis;
  overflow: hidden;
  white-space: nowrap;
  display: flex;
}
</style>
