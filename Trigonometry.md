1° = PI / 180°
Golden Angle = 137.5077640844293


```
let totalPetals = 360;

let rotation = 0;
let angle = 90;
let goldenAngle = 137.5077640844293;

let radius = 100;
let ToRadians = 3.14 / 180;

let radiusGrowth = 1.0049;

function setup() {
  width = 1200;
  height = 600;
  createCanvas(width, height);
  noStroke();
  smooth();
  background(0);
  
  for(i = 0; i < totalPetals; i++) {
    rotation += goldenAngle;
    radius *= radiusGrowth;
    let petal = new Petal();
    petal.x = width / 2 + radius * (cos(rotation * ToRadians));
    petal.y = height / 2 + radius * (sin(rotation * ToRadians));
    petal.rotation = rotation * ToRadians;
    petal.scale += (i*2) / totalPetals;
    petal.render();
  }
}

class Petal {
  constructor() {
    this.x = 0;
    this.y = 0;
    this.rotation = 0;
    this.scale = 1;
    
    this.baseColor = (0, 255, 255);
    this.detailColor = (255, 255, 255);
    this.trimColor = (0, 0, 0);
  }
  
  render() {
    push();
    translate(this.x, this.y);
    fill(this.baseColor);
    rotate(this.rotation);
    scale(this.scale, this.scale);
    rect(-10, -1, 20, 2);
    ellipse(0, 0, 10, 10);
    fill(this.detailColor);
    ellipse(0, 0, 8, 8);
    fill(this.trimColor);
    ellipse(0,0,5,5);
    
    pop();
  }
}
```