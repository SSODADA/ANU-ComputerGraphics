# ANU-ComputerGraphics
## 24년 안동대 컴퓨터 그래픽스 과제 지형 만들기 및 작품

20210814 백경민

noise를 활용하여 지형이 만들어 지는게 신기하였고 또한 도형을 만들어보고 여기에 재질과 조명을 넣는 이 과제를 수행하며 겪었던 어려움도 있었지만, 그 과정에서 문제를 해결하기 위해 다양한 접근 방식을 시도해보고, 필요한 지식을 찾아보며 학습하는 과정 자체가 매우 유익했습니다. 이를 통해 프로그래밍 능력뿐만 아니라, 독립적인 학습 능력과 문제 해결 능력도 함께 향상시킬 수 있었습니다. 앞으로도 이러한 경험을 바탕으로 더 많은 프로젝트에 도전하며, 계속해서 새로운 지식을 탐구하고 습득해 나가는 것이 중요하다고 생각들었습니다.

------------------------------------------------------------------------------

# 실행화면

<img src="https://private-user-images.githubusercontent.com/80105027/320191325-f211dd53-2c40-4da3-8236-a1bae22ab091.gif?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTUiLCJleHAiOjE3MTIzOTA3MzEsIm5iZiI6MTcxMjM5MDQzMSwicGF0aCI6Ii84MDEwNTAyNy8zMjAxOTEzMjUtZjIxMWRkNTMtMmM0MC00ZGEzLTgyMzYtYTFiYWUyMmFiMDkxLmdpZj9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFWQ09EWUxTQTUzUFFLNFpBJTJGMjAyNDA0MDYlMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjQwNDA2VDA4MDAzMVomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPWZkZWZiMGEwMWZlYTkyYTU3MGZkYWU0MTYzNDIxZDc2ZDNhNjc0NWNkN2M5ZjMxMTlmZDQxZWY0OTdkYzJlZjkmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.WeME6M7CJzkj5w76xwSuffUpO8mSjtBuPRB1qu7Vx2E">

-----------------------------------------------------------------------------
# 과제코드

    // Daniel Shiffman
    // http://codingtra.in
    // https://youtu.be/IKB1hWWedMk
    // https://thecodingtrain.com/CodingChallenges/011-perlinnoiseterrain.html
    
    // Edited by SacrificeProductions
    // Modified by Dr. Jaechang Shim
    function setup() {
      createCanvas(800, 800, WEBGL);
      cols = w / scl;
      rows = h / scl;
    
      for (let x = 0; x < cols; x++) {
        terrain[x] = [];
        for (let y = 0; y < rows; y++) {
          terrain[x][y] = 0; //specify a default value for now
        }
      }
    }
    
    let cols, rows;
    let scl = 20;
    let w = 1400;
    let h = 1000;
    
    let ships = [];
    let shipVisible = false;
    let shipSpeed = 2;
    let shipX = 0;
    let shipY = 0; 
    
    let flying = 0;
    
    let terrain = [];
    
    function draw() {
      orbitControl()
      noStroke();
      flying -= 0.03;
      let yoff = flying;
      for (let y = 0; y < rows; y++) {
        let xoff = 0;
        for (let x = 0; x < cols; x++) {
          terrain[x][y] = map(noise(xoff, yoff), 0, 1, -100, 100);
          xoff += 0.2;
        }
        yoff += 0.2;
      }
      background(40, 208, 255);
      rotateX(PI / 3);
      fill(0, 128, 255, 150);
      translate(-w / 2, -h / 2);
      for (let y = 0; y < rows - 1; y++) {
        beginShape(TRIANGLE_STRIP);
        for (let x = 0; x < cols; x++) {
          vertex(x * scl, y * scl, terrain[x][y]);
          vertex(x * scl, (y + 1) * scl, terrain[x][y + 1]);
        }
        endShape();
      }
    
      // 배가 지형과 충돌하지 않도록 배의 높이를 조정하는 로직 추가
      for (let i = ships.length - 1; i >= 0; i--) {
        let shipXIndex = floor(ships[i].x / scl);
        let shipYIndex = floor(ships[i].y / scl);
    
        // 배의 x, y 위치에 해당하는 지형의 최대 높이를 찾습니다.
        let maxHeight = terrain[shipXIndex][shipYIndex];
        for (let dx = -1; dx <= 1; dx++) {
          for (let dy = -1; dy <= 1; dy++) {
            let nx = shipXIndex + dx;
            let ny = shipYIndex + dy;
            if (nx >= 0 && nx < cols && ny >= 0 && ny < rows) {
              maxHeight = max(maxHeight, terrain[nx][ny]);
            }
          }
        }
    
        // 배의 z 위치를 조정하여 지형 위에 떠있도록 설정합니다.
        let shipZ = maxHeight + 100; // 지형보다 100 높게 설정
    
        push();
        translate(w / 2, h / 2);
        // 배의 x, y 위치를 조정하고, z 위치를 shipZ로 설정합니다.
        translate(ships[i].x - width / 2, (ships[i].y - height / 2) * 1, shipZ);
        scale(0.1);
        rotateX(-HALF_PI);
        let dirX = (mouseX / width - 0.5) * 2;
        let dirY = (mouseY / height - 0.5) * 2;
        directionalLight(255, 255, 255, -dirX, -dirY, -1);
        ship();
        pop();
    
        ships[i].y += shipSpeed;
        
        if (ships[i].y - height / 2 > height) {
          ships.splice(i, 1);
        }
      }
    }
    
    function wood() {
      fill(160, 82, 45);
      rotateX(HALF_PI);
      for(let i = -240; i <= 240; i += 80) {
        for(let j = 0; j <= 400; j += 400) {
          push();
          translate(i, j);
          cylinder(40, 400);
          pop();
        }
      }
    }
    
    function flag() {
      noStroke();
      translate(0, -320, 200);
      fill(0); 
      cylinder(15, 700);
      push(); 
      rotateY(HALF_PI + sin(frameCount * 0.1) * 0.5);  
      normalMaterial(); 
      translate(150, -240, 0); 
      plane(300, 200); 
      pop(); 
    }
    
    function ship() {
      push();
      wood();
      pop();
      push();
      flag();
      pop();
    }
    
    function mousePressed() {
      ships.push({x: mouseX, y: mouseY});
      if (!shipVisible) { 
        shipVisible = true;
        shipX = mouseX; 
        shipY = mouseY;
        shipStartY = shipY; 
      }
    }
