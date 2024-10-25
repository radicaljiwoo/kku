<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>약 봉지 정보 및 약국 위치</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background-color: #f8f9fa;
            color: #343a40;
            margin: 0;
            padding: 20px;
            display: flex;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
        }
        .container {
            width: 100%;
            max-width: 750px;
            background-color: #ffffff;
            padding: 25px;
            border-radius: 12px;
            box-shadow: 0px 4px 12px rgba(0, 0, 0, 0.1);
            position: relative;
        }
        h1 {
            font-size: 26px;
            font-weight: bold;
            color: #007bff;
            margin-bottom: 20px;
            text-align: center;
        }
        h2 {
            font-size: 18px;
            font-weight: 600;
            color: #495057;
            margin-top: 20px;
            text-align: center;
        }
        .info-table {
            width: 100%;
            margin-top: 20px;
            border-collapse: collapse;
        }
        .info-table th, .info-table td {
            padding: 12px;
            text-align: left;
        }
        .info-table th {
            color: #6c757d;
            font-weight: 500;
        }
        .info-table td {
            color: #343a40;
            background-color: #f1f3f5;
            border-radius: 8px;
            padding: 12px;
        }
        .warning {
            color: #d9534f;
        }
        #map {
            width: 100%;
            height: 250px;
            margin-top: 20px;
            border-radius: 8px;
            box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.1);
        }
        .voice-button {
            padding: 12px 20px;
            font-size: 22px;
            background-color: #007bff;
            color: #ffffff;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 0px 4px 8px rgba(0, 123, 255, 0.2);
            display: block;
            margin: 20px auto;
            text-align: center;
            width: 80%;
        }
        .voice-button:hover {
            background-color: #0056b3;
            box-shadow: 0px 6px 12px rgba(0, 86, 179, 0.2);
            transform: scale(1.05);
        }
        .image-section {
            margin-top: 20px;
            text-align: center;
        }
        .image-section img {
            max-width: 100%;
            height: auto;
            border-radius: 8px;
            box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.1);
        }
        @media (max-width: 600px) {
            h1 {
                font-size: 22px;
            }
            h2 {
                font-size: 16px;
            }
            .voice-button {
                font-size: 18px;
                padding: 10px 15px;
            }
        }
    </style>
</head>
<body>

<div class="container">
    <h1>약 봉지 정보 및 약국 위치</h1>
    
    <!-- 전화번호 추가 -->
    <p style="text-align: center; font-size: 18px;">
        약국 전화번호: <a href="tel:0328611181" style="color: #007bff; text-decoration: none;">032-861-1181</a>
    </p>    


    <h2>약물 이름: [씨엠지세파클러캡슐, 디프로펜정, 상남아세트아미노펜, 가리온정10, 유한멕사메타손정]</h2>
    
    <button class="voice-button" onclick="speakInfo()">🔊 음성 듣기</button>

    <div class="image-section">
        <h2>약 사진</h2>
        <img src="https://ifh.cc/g/7qzQHm.jpg" alt="약 사진">
    </div>

    <table class="info-table">
        <tr>
            <th>조제 일자</th>
            <td id="preparationDate">[2024년 10월 24일]</td>
        </tr>
        <tr>
            <th>처방 기관</th>
            <td id="institution">[처방기관정보(종별)는 의원이며 조제기관정보(위치)는 인천광역시 미추홀구 입니다.]</td>
        </tr>
        <tr>
            <th>효능</th>
            <td id="effect">[알르레기성 비염을 치료하고 열을 내리고 통증을 해소하는 약입니다.]</td>
        </tr>
        <tr>
            <th>조제비</th>
            <td id="cost">[약제비 총액은 14300원이며 본인부담금은 4280원입니다.]</td>
        </tr>
        <tr>
            <th>복용 권장 시간</th>
            <td id="recommendedTime">[아침 점심 저녁 식사 후 복용하는 것을 권장합니다.]</td>
        </tr>
        <tr>
            <th>병용금지 성분</th>
            <td class="warning" id="prohibitedIngredients">[디프로펜정을 복용할시 다른 소염진통제를 병용하면 안됩니다.]</td>
        </tr>
        <tr>
            <th>주의사항</th>
            <td id="precautions">[처방기간을 준수하고 수유주의, 알코올류 주의, 졸음주의가 필요합니다. 또한 유한멕사메타손정을 복용시 정기적 혈당검사가 필요합니다.]</td>
        </tr>
    </table>

    <h2>주변 약국 위치</h2>
    <div id="map"></div>
</div>

<script>
    function initMap() {
        if (navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(
                function (position) {
                    const userLocation = {
                        lat: position.coords.latitude,
                        lng: position.coords.longitude
                    };

                    const map = new google.maps.Map(document.getElementById("map"), {
                        center: userLocation,
                        zoom: 15
                    });

                    new google.maps.Marker({
                        position: userLocation,
                        map: map,
                        title: "현재 위치"
                    });

                    const request = {
                        location: userLocation,
                        radius: '2000',
                        type: ['pharmacy']
                    };

                    const service = new google.maps.places.PlacesService(map);
                    service.nearbySearch(request, function (results, status) {
                        if (status === google.maps.places.PlacesServiceStatus.OK) {
                            results.forEach(place => {
                                new google.maps.Marker({
                                    position: place.geometry.location,
                                    map: map,
                                    title: place.name
                                });
                            });
                        }
                    });
                },
                function () {
                    alert("위치 정보를 사용할 수 없습니다.");
                }
            );
        } else {
            alert("브라우저에서 위치 정보를 지원하지 않습니다.");
        }
    }

    // 음성으로 정보 읽기 기능
    function speakInfo() {
        const name = "씨엠지세파클러캡슐, 디프로펜정, 상남아세트아미노펜, 가리온정10, 유한멕사메타손정";
        const preparationDate = document.getElementById("preparationDate").innerText;
        const institution = document.getElementById("institution").innerText;
        const effect = document.getElementById("effect").innerText;
        const cost = document.getElementById("cost").innerText;
        const recommendedTime = document.getElementById("recommendedTime").innerText;
        const prohibitedIngredients = document.getElementById("prohibitedIngredients").innerText;
        const precautions = document.getElementById("precautions").innerText;

        const text = `
            약물 이름 ${name}
            조제 일자 ${preparationDate}
            처방 기관 ${institution}
            이 약의 효능 ${effect}
            조제비 ${cost}
            복용 권장 시간 ${recommendedTime}
            병용금지 성분 ${prohibitedIngredients},
            주의사항 ${precautions}
        `;

        const speech = new SpeechSynthesisUtterance(text);
        speech.lang = "ko-KR"; 
        window.speechSynthesis.speak(speech);
    }
</script>

<!-- Google Maps JavaScript API 로드 -->
<script src="https://maps.googleapis.com/maps/api/js?key=YOUR_API_KEY&libraries=places&callback=initMap" async defer></script>

</body>
</html>
