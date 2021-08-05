<!DOCTYPE html>
<html lang="ja">
  <head>
    <meta charset="utf-8">
    <title>Google Maps World Heritage(Japan)</title>
    <style>
      // 省略
    </style>
  </head>
  <body>
    <div id="header">Google Maps - World Heritage(Japan) - 日本の世界遺産</div>
    <div id="target"></div>
    <td><div id="sidebar"></div></td>

    <!-- Google MAP API呼出 -->
    <script src="https://maps.googleapis.com/maps/api/js?language=ja&region=JP&key=「APIキーを設定する」&callback=initMap" async defer></script>

    <script>
      var map;
      var marker = [];
      var infoWindow = [];

      function initMap() {

        var target = document.getElementById('target');
        var centerp = {lat: 37.67229496806523, lng: 137.88838989062504};

        //マップ表示
        map = new google.maps.Map(target, {
          center: centerp,
          zoom: 5,
        });

        //APIにてデータを取得して、位置とマーカーをセットするfunctionを呼び出す
        var request = new XMLHttpRequest();
        request.open('GET', 'https://api.sheetson.com/v1/sheets/sheet1?spreadsheetId=1h_rneeHeYPDNbK1U5AZXdDlpFBPbVD7W6QVdXHDKMrw', true);
        request.responseType = 'json';

        request.onload = function () {
          var markerData = this.response;
          // console.log(markerData);
          setData(markerData);
        };

        request.send();

      }

      // 位置とマーカーをセットするfunction
      function setData(markerData){

        var sidebar_html = "";
        var icon;

        for (var i = 0; i < markerData.results.length; i++) {
          // マーカー位置セット
          var markerLatLng = new google.maps.LatLng({
            lat: Number(markerData.results[i]['lat']),
            lng: Number(markerData.results[i]['lng'])
          });
          // マーカーアイコンのセット
          if (markerData.results[i]['pic'] === 'on'){
            icon = new google.maps.MarkerImage('./icon_blue/numbericon_blue_' + [i + 1] + '.png');
          } else {
            icon = new google.maps.MarkerImage('./icon_pink/numbericon_pink_' + [i + 1] + '.png');
          }
          // マーカーのセット
          marker[i] = new google.maps.Marker({
            position: markerLatLng,          // マーカーを立てる位置を指定
            map: map,                        // マーカーを立てる地図を指定
            icon: icon                       // アイコン指定
          });
          // 吹き出しの追加
          var name = markerData.results[i]['name'];
          var url = markerData.results[i]['url'];
          var tag = markerData.results[i]['tag'];
          var img = markerData.results[i]['img'];

          if (markerData.results[i]['pic'] === 'on'){
            var setHtml = '<a href=' + url + ' target="right">' + name + '</a><br><br>' + tag + '<br><img src=' + img + ' width="200" height="150"><br>'
          } else {
            var setHtml = '<a href=' + url + ' target="right">' + name + '</a><br><br>' + tag + '<br><br>'
          }

          infoWindow[i] = new google.maps.InfoWindow({
            content: setHtml           // 吹き出しに表示する内容
          });
          // サイドバー
          sidebar_html += '<a href="javascript:myclick(' + i + ')">' + name + '<\/a><br />';
          // マーカーにクリックイベントを追加
          markerEvent(i);
        }

        // サイドバー
        document.getElementById("sidebar").innerHTML = '<日本の世界遺産List><br><br>' + sidebar_html;

      }

      var openWindow;

      // クリックイベント
      function markerEvent(i) {
        marker[i].addListener('click', function() {
          myclick(i);
        });
      }

      // 吹き出しのオープン・クローズ
      function myclick(i) {
        if(openWindow){
          openWindow.close();
        }
        infoWindow[i].open(map, marker[i]);
        openWindow = infoWindow[i];
      }

    </script>

  </body>
</html>
