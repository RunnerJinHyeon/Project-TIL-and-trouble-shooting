## API 받아오기
```javascript
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
### TIL
- useState,useEffect,Axios 를 사용해서 한국건강증진개발원의 운동목록을 받아오는 컴포넌트이다.
- get요청으로 받아온 json데이터를 ExerciseData라는 상태에 setExerciseData라는 상태변경함수를 사용하여 넣어주었다.
- get요청때 넣어주는 url안에 page,perpage,returntype과 같은 옵션을 설정해주면 받아오는 데이터의 갯수,데이터 타입등을 변경가능.

### Trouble
- 받아온 데이터가 data라는 껍데기로 한번더 감싸져있는 형태였기 때문에 컴포넌트의 return문안에 단순히 Exercise를 map메소드를 활용하여 화면에 띄우려고 했을때 에러가 생겼다. ExerciseData.data로 문제해결.
- 또한 받아온 데이터에 id와같은 각각의 데이터를 구분해주는 요소가 없어서 div안의 키값을 데이터의 인덱스값인 i를 할당햐여 최적화시켜주었다. 데이터의 뒤쪽에 또다른데이터가 추가된다면 상관없겠지만 인덱스0번의 앞에 데이터가 새로 생길떄 최적화문제가 생길 여지가 있다. 이는 좀더 좋은방법을 찾아봐야겠다.
