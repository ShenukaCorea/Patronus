# Patronus
```javascript
//calling up the video
import processing.video.*;

Capture video;

PImage prevFrame;

float threshold = 150;
int Mx = 0;
int My = 0;
int ave = 0;

//positioning ball
int ballX = width/2;
int ballY = height/2;
int rsp = 5;

//declare arraylist for magic particles
ArrayList<particle> particleArray;
particle p;

void setup() {
  size(640, 480);
  //streaming video
  video = new Capture(this, width, height, 30);
  video.start();
  prevFrame = createImage(video.width, video.height, RGB);

  //create array for magic particles
  particleArray = new ArrayList<particle>();
}

void draw() {

  //drawing each frame of the video
  if (video.available()) {

    prevFrame.copy(video, 0, 0, video.width, video.height, 0, 0, video.width, video.height); 
    prevFrame.updatePixels();
    video.read();
  }

  loadPixels();
  video.loadPixels();
  prevFrame.loadPixels();

  Mx = 0;
  My = 0;
  ave = 0;

  //detect where there has been motion using pixel colour
  for (int x = 0; x < video.width; x ++ ) {
    for (int y = 0; y < video.height; y ++ ) {

      int loc = x + y*video.width;            
      color current = video.pixels[loc];      
      color previous = prevFrame.pixels[loc]; 


      float r1 = red(current); 
      float g1 = green(current); 
      float b1 = blue(current);
      float r2 = red(previous); 
      float g2 = green(previous); 
      float b2 = blue(previous);
      float diff = dist(r1, g1, b1, r2, g2, b2);


      if (diff > threshold) { 
        pixels[loc] = video.pixels[loc];
        Mx += x;
        My += y;
        ave++;
      } else {

        pixels[loc] = video.pixels[loc];
      }
    }
  }
  fill(255);
  rect(0, 0, width, height);
  if (ave != 0) { 
    Mx = Mx/ave;
    My = My/ave;
  }
  //move ball where there has been motion
  if (Mx > ballX + rsp/2 && Mx > 50) {
    ballX+= rsp;
  } else if (Mx < ballX - rsp/2 && Mx > 50) {
    ballX-= rsp;
  }
  if (My > ballY + rsp/2 && My > 50) {
    ballY+= rsp;
  } else if (My < ballY - rsp/2 && My > 50) {
    ballY-= rsp;
  }

  updatePixels();
  noStroke();
  
  fill(50,0,150,60);
    rect(0,0,640, 480);


  fill(200, 200, 255, 100);
  ellipse(ballX, ballY, 40, 40);
    fill(200, 200, 255, 50);
  ellipse(ballX, ballY, 50, 50);
      fill(200, 200, 255, 50);
  ellipse(ballX, ballY, 75, 75);
    
  fill(255, 255, 255, 200);
  ellipse(ballX, ballY, 25, 25);


  //insert particles at the posistion of ball

  for (int i=0; i<10; i++) {
    particle p = new particle(ballX, ballY);
    particleArray.add(p);
  }
  //display particles
  for (int i=0; i<particleArray.size(); i++) {
    particle p = particleArray.get(i);
    p.display(); 
    //remove particle if it leaves range
    if (p.location.y>ballY+50 || p.location.y<ballY-50|| p.location.x>ballX+50|| p.location.x<ballX-50) {
      particleArray.remove(i);
    }
  }
}

class particle {
  PVector location;

  particle(float x, float y) {
    location = new PVector(x, y);
  }

  void display() {
    float radius;
    radius= random(1, 20);
    noStroke();
    //fill(200, 200, 255, 50);
    //float  randomrad= random(1, 15);
    //ellipse(location.x+random(1, 50), location.y+random(1, 50), randomrad, randomrad);
    fill(200, 200, 255, 5);
    ellipse(location.x, location.y, radius+10, radius+10);
    fill(255, 255, 255, 50);
    ellipse(location.x, location.y, radius, radius);
  }
}
```
