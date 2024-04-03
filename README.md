# ANU-ComputerGraphics
# 24년 그래픽스 과제
# 20210814 백경민

------------------------------------------------------------------------------
과제 코드입니다
------------------------------------------------------------------------------
// Daniel Shiffman
// http://codingtra.in
// https://youtu.be/IKB1hWWedMk
// https://thecodingtrain.com/CodingChallenges/011-perlinnoiseterrain.html

// Edited by SacrificeProductions
// Modified by Dr. Jaechang Shim

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

function draw() {
  orbitControl()
  noStroke();
  flying -= 0.05;
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
  
  
  for (let i = ships.length - 1; i >= 0; i--) {
    push();
    translate(w / 2, h / 2);
    translate(ships[i].x - width / 2, (ships[i].y - height / 2) * 1);
    scale(0.2);
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
  if (!shipVisible) { 
    shipVisible = true;
    shipX = mouseX; 
    shipY = mouseY;
    shipStartY = shipY; 
  }
}

function mousePressed() {
  ships.push({x: mouseX, y: mouseY});
}
