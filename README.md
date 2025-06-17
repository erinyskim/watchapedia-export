# Watchapedia export
- 요즘 왓챠 기사가 많이 나와서 데이터 백업하려고 예전 [익무글](https://extmovie.com/movietalk/37623288)을 찾아봤는데 막혀버렸네요.
- 그래서 직접 만들었습니다. 저와 같은 분들이 많으실 것 같아서 관련 방법 공유합니다.

### 백업 방법
1. 크롬에서 왓챠피디아 웹사이트에 로그인합니다. https://pedia.watcha.com
    <img width="650" src="https://raw.githubusercontent.com/erinyskim/watchapedia-export/main/img/export_001.png">
2. 왓챠피디아 주소 창에 javascript:를 입력하고(:콜론 까지 입력, 엔터치면 **안됩니다**.)
    <img width="650" src="https://raw.githubusercontent.com/erinyskim/watchapedia-export/main/img/export_002.png">
3. **아래 코드 복사** 또는 [watchapedia_export.js](https://github.com/erinyskim/watchapedia-export/blob/main/watchapedia_export.js) 링크 클릭 후 스크립트를 복사하고 2번에서 입력한 `javascript:` 바로 뒤에 붙여넣기 한 뒤에 엔터 키를 입력해주세요.
  ```javascript
!async function(){const e=document.querySelector('a[href*="/users/"]').href.replace(/^.*\/users\/(.*)$/,"$1"),t=async e=>fetch(`https://api-pedia.watcha.com${e}`,{credentials:"same-origin",headers:{"x-watcha-client":"watcha-WebApp","x-watcha-client-language":"ko","x-watcha-client-region":"KR","x-watcha-client-version":"2.1.0"}}).then((e=>e.json())).then((e=>e.result)),n=e=>{const t=e.content.code,n=`https://pedia.watcha.com/ko-KR/contents/${t}`,a=e.content.title.replace(/"/g,""),i="tv_seasons"===e.content.content_type?"TV":"MOVIE",c=e.content.year,s=e.content.director_names?.join(),r=e.user_content_action.watched_at||e.created_at,o=+e.user_content_action.rating/2,l=e.text?.trim().replace(/"/g,'""');return{id:t,url:n,title:a,type:i,year:c,directors:s,watchedAt:r,rating:o,review:l,spoiler:e.spoiler}},a=async(e,a)=>{let i=await t(e);const c=[];i.result.forEach((e=>{c.push(n(e))}));let s=i.next_uri;for(;s;)i=await t(s),i.result.forEach((e=>{c.push(n(e))})),a&&a(i.result.length),s=i.next_uri;return c},i=async(t,n)=>{let i=0;const c=e=>{i+=e,n&&n(i)},s=await a(`/api/users/${e}/contents/${t}/ratings`,c),r=await a(`/api/users/${e}/contents/${t}/comments`,c);return s.map((e=>{const t=r.find((t=>t.id===e.id));let n=t?.watchedAt||e.watchedAt;if(n){const e=new Date(n);n=`${e.getFullYear()}-${`0${e.getMonth()+1}`.slice(-2)}-${`0${e.getDate()}`.slice(-2)}`}return[e.id,e.url,e.title,e.type,e.year,t?.directors||e.directors,n,e.rating,t?.review,t?.spoiler]}))};await(async()=>{const{setProgress:n,destroy:a}=(()=>{const e=Object.assign(document.createElement("div"),{style:"position: fixed; left: 0; top: 0; right: 0;height: 59px; z-index:60;display:flex; align-items: center; background-color:#fff"}),t=Object.assign(document.createElement("div"),{style:"max-width:1320px;width:100%;height:100%;margin:0 auto;display:flex; align-items: center;padding:0 32px;"}),n=Object.assign(document.createElement("p"),{style:"color: #7e7e7e; font-size: 15px; letter-spacing: -0.3px;"});n.innerText="리뷰 다운로드 중입니다.";const a=Object.assign(document.createElement("span"),{style:"font-weight: bold"});return n.appendChild(a),t.appendChild(n),e.appendChild(t),document.body.appendChild(e),{setProgress(e){a.innerText=`${e.toFixed(0)}%`},destroy(){e.remove()}}})();try{const a=await(async()=>(await t(`/api/users/${e}`)).ratings_count)(),c=(e=0)=>{a&&e&&n(e/a*100)},s=await i("movies",c),r=await i("tv_seasons",c);let o=s.map((e=>`"${e.join('","')}"`)).join("\n");o+="\n",o+=r.map((e=>`"${e.join('","')}"`)).join("\n"),((e,t)=>{const n=new window.Blob(["\ufeff"+t],{type:"text/csv;charset=utf-8;"}),a=document.createElement("a");a.href=URL.createObjectURL(n),a.download=e,a.click()})(`${e}-watcha.csv`,`ID,URL,Title,Type,Year,Directors,WatchedAt,Rating,Review,Spoiler,\n${o}`)}catch(e){}a()})()}();
  ```

  <img width="650" src="https://raw.githubusercontent.com/erinyskim/watchapedia-export/main/img/export_004.png">
4. 잠시만 기다리면 내 기록이 파일로 다운로드됩니다. 

### 참고사항
- 왓챠피디아 계정이 '비공개' 또는 '친구 공개'인 경우 '전체 공개'로 변경해야만 모든 데이터를 다운로드 받을 수 있습니다.
- 본 날짜(WatchedAt)는 왓챠피디아에 코멘트를 작성한 경우에만 기록됩니다.

### Demo 영상
https://www.loom.com/share/dc1babd208ae4aa89d7b95a5b1c78f24
