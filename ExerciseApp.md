<br>

## **Project. Exercise Tracker**
>**[코드스테이츠 FE 45기] 스터디_빈.진.우(beenzinoo)** <br>
>**개발기간 : 2023.7.17 ~ 2023.08.5**

<br>

## 프로젝트팀 소개
|<center>윤 예빈</center>|<center>이 인우</center>|<center>김 진현</center>|<center>최 한빈</center>|<center>진 종환</center>|
|---|---|---|---|---|
|<img width="150px" height="150px" src =  "images/Yebin.jpg">|<img width="150px" height="150px" src = "images/Inwoo.jpg">|<img width="150px" height="150px" src = "images/Jinhyeon.jpg">|<img width="150px" height="150px" src = "images/Hanbin.jpg">|<img width="150px" height="150px" src = "images/Jonghwan.png">|
|<center>[@YebinYun](https://github.com/YebinYun)</center>|<center>[@dpftlel21](https://github.com/dpftlel21)</center>|<center>[@RunnerJinHyeon](https://github.com/RunnerJinHyeon)</center>|<center>[@hanbinchoi](https://github.com/hanbinchoi)</center>|<center>[@MSbtff](https://github.com/MSbtff)</center>|

<br>

## 프로젝트 소개

<br>

## 주요 기능
### 기능, 제공, 정보 등등
- 💪
- 💪

### 기능, 제공, 정보 등등
- 💪
- 💪

### 기능, 제공, 정보 등등
- 💪
- 💪

<br>

## **Stack**
### **Environment**
<img src="https://img.shields.io/badge/visual studio code-007ACC?style=flat&logo=visualstudiocode&logoColor=white"/> <img src="https://img.shields.io/badge/git-F05032?style=flat&logo=git&logoColor=white"/> <img src="https://img.shields.io/badge/git hub-181717?style=flat&logo=github&logoColor=white"/>

### **Config**
<img src="https://img.shields.io/badge/npm-CB3837?style=flat&logo=npm&logoColor=white"/>

### **Development**
<img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=flat&logo=JavaScript&logoColor=white"/> <img src="https://img.shields.io/badge/Tailwind CSS-06B6D4?style=flat&logo=Tailwind CSS&logoColor=white"/> <img src="https://img.shields.io/badge/redux-764ABC?style=flat&logo=redux&logoColor=white"/> <img src="https://img.shields.io/badge/react router-CA4245?style=flat&logo=reactrouter&logoColor=white"/>

<br>

## 아키텍쳐
### 디렉토리 구조


<br>





# 내가 맡은 기능구현과 트러블슈팅
## 운동 API 받아오기
```javascript
//Exercise.js
import axios from 'axios';
import React from 'react';
import {useEffect, useState} from 'react';

function Exercise() {
  const [ExerciseData, setExerciseData] = useState([]);

  useEffect(() => {
    axios
      .get(
        'https://api.odcloud.kr/api/15068730/v1/uddi:2dd1a2cb-6030-48a2-980d-c31f0cc18b6c?page=1&perPage=10&returnType=JSON&serviceKey={apikey}'
      )
      .then((response) => {
        console.log(response.data); // 받은 데이터 확인
        setExerciseData(response.data); // 받은 데이터로 운동 상태 업데이트
      })
      .catch((error) => {
        console.log('에러:', error);
      });
  }, []);

  //괄호 주의
  return (
    <div>
      {ExerciseData.data.map((item, i) => (
        <div key={i}>
          <p>운동명: {item['운동명']}</p>
          <p>MET계수: {item['MET계수']}</p>
        </div>
      ))}
    </div>
  );
}

export default Exercise;



```
### ✅ TIL
- useState,useEffect,Axios 를 사용해서 한국건강증진개발원의 운동목록을 받아오는 컴포넌트이다.
- get요청으로 받아온 json데이터를 ExerciseData라는 상태에 setExerciseData라는 상태변경함수를 사용하여 넣어주었다.
- get요청때 넣어주는 url안에 page,perpage,returntype과 같은 옵션을 설정해주면 받아오는 데이터의 갯수,데이터 타입등을 변경가능.

### 🔧 Trouble
- 받아온 데이터가 data라는 껍데기로 한번더 감싸져있는 형태였기 때문에 컴포넌트의 return문안에 단순히 Exercise를 map메소드를 활용하여 화면에 띄우려고 했을때 에러가 생겼다. ExerciseData.data로 문제해결.
- 또한 받아온 데이터에 id와같은 각각의 데이터를 구분해주는 요소가 없어서 div안의 키값을 데이터의 인덱스값인 i를 할당햐여 최적화시켜주었다. 데이터의 뒤쪽에 또다른데이터가 추가된다면 상관없겠지만 인덱스0번의 앞에 데이터가 새로 생길떄 최적화문제가 생길 여지가 있다. 이는 좀더 좋은방법을 찾아봐야겠다.




## 카카오 지도 불러오기
```javascript
//MapContainer.js
import React, {useEffect,useState} from 'react';

const {kakao} = window;

const MapContainer = ({searchPlace}) => {
  //받아온 위도,경도 상태변경
  const [latitude , setLatitude] = useState(null);
  const [longitude , setLongitude] = useState(null);

  // 검색 결과 상태변경

  const [Places , setPlaces] = useState([]);


  useEffect(() => {
    
    // 현재 위치 받아오기  (리액트에 내장된 api)
    const getLocation = () =>{
      if(navigator.geolocation){
        navigator.geolocation.getCurrentPosition(
          (position)=>{
            setLatitude(position.coords.latitude);
            setLongitude(position.coords.longitude);
          },
          (error)=>{
            console.error('cannot find location',error);
          }
        )
      }else{
        console.error('Geolocation is not supported by this browser.');
      }
    };
    getLocation();

    
    // 카카오 맵 API
    let infowindow = new kakao.maps.InfoWindow({zIndex: 1});
    const container = document.getElementById('myMap');
    const options = {
      center: new kakao.maps.LatLng(latitude, longitude),//현재위치 입력
      level: 3,
    };
    const map = new kakao.maps.Map(container, options);

    const ps = new kakao.maps.services.Places();

    ps.keywordSearch(searchPlace, placesSearchCB);

    function placesSearchCB(data, status) {
      if (status === kakao.maps.services.Status.OK) {
        let bounds = new kakao.maps.LatLngBounds();

        for (let i = 0; i < data.length; i++) {
          displayMarker(data[i]);
          bounds.extend(new kakao.maps.LatLng(data[i].y, data[i].x));
        }

        map.setBounds(bounds);
        setPlaces(data)
        console.log(Places)
      }
    }

    

    // 지도 마커 표시 
    function displayMarker(place) {
      let marker = new kakao.maps.Marker({
        map: map,
        position: new kakao.maps.LatLng(place.y, place.x),
      });

      // 마커에 클릭이벤트 등록
      kakao.maps.event.addListener(marker, 'click', function () {
        // 마커를 클릭시 장소명 인포윈도우에 표출
        infowindow.setContent(
          '<div style="padding:5px;font-ize:12px;">' +
            place.place_name +
            '</div>'
        );
        infowindow.open(map, marker);
      });
    }
  }, [searchPlace,latitude,longitude]);

  return (
    <>
    <div
      id="myMap"
      style={{
        width: '500px',
        height: '500px',
      }}
    ></div>
    <div id="result-list">
        {Places.map((item, i) => (
          <div key={i} style={{ marginTop: '20px' }}>
            <span>{i + 1}</span>
            <div>
              <h5><a href={item.place_url}>{item.place_name}</a></h5>
              {item.road_address_name ? (
                <div>
                  <span>{item.road_address_name}</span>
                  <a href={item.place_url}></a>
                  <span>{item.address_name}</span>
                </div>
              ) : (
                <span>{item.address_name}</span>
              )}
              <span>{item.phone}</span>
            </div>
          </div>
        ))}
        <div id="pagination"></div>
      </div>
      </>
  );
};

export default MapContainer;

```

```html
<script
      type="text/javascript"
      src="//dapi.kakao.com/v2/maps/sdk.js?appkey=%REACT_APP_KAKAOMAP_KEY%&libraries=services"
    ></script>
```
## 지도 API 불러오기
### ✅ TIL
- index.html안에 script 태그를 사용하여 카카오지도 API를 받아온 후 MapContainer.js안에 kakao.maps메소드로 지도상의 마커와 함꼐 표시가 되도록 return문 안에 div로 표현.
- MapContainer.js의 상위 컴포넌트에서 받아온 지역 검색 값을 지도상에 placesSearchCB 함수로 보여줌.

### 🔧 Trouble
- 지도를 켰을 때 현재 사용자의 위치가 바로 지도상에 표시되도록 하기 위해 현재위치정보를 받아올 수 있는 Geolocation API를 사용하여 latitude와 longitude에 각각 위도와 경도값을 상태변경 해준 후 option값에 넣어줬으나 실제로 지도를 켰을때 적용되지 않았다. 
- useEffect의 의존성 배열에 latitude,longitude값을 넣어주어야 사용자의 위치값을 받아온 후 상태가 변경된 latitude, longitude 가 적용, 리랜더링 되어 실제 지도에 사용자의 위치가 정중앙에 표시된다.


<br>

## .env 파일 gitignore
### 🔧 Trouble
- 카카오api키, 구글로그인api키 등등이 담긴 .env파일을 맨처음 pull request 할때 실수로 메인 repository에 push한 채로 PR이 되어 깃허브에 올라가버렸다.
- 이후 gitignore에 .env 기입 후 다시 push를 해봤지만 여전히 메인 branch에 남아있어 GitGuardian에게서 위험메일이 왔다.
- 다행히 깃허브 자체적으로 파일을 삭제후 삭제된 상태를 커밋상태로 되게 해주었다.
- 앞으론 gitignore 필수!!



