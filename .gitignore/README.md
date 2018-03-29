# large-system

var input, inputone, inputtwo, button, greeting, greetingone, greetingtwo;
var REMOTE_PORT = undefined;
var LOCAL_PORT = undefined;
var IPADDRESS = undefined;
var client = undefined;
var oscServer = undefined;
var colorchange = undefined;

var osc = require('node-osc');

var x = undefined;
var y = undefined;
var z = undefined;

function setup() {
 	createCanvas(displayWidth, displayHeight);
	strokeWeight(3);
	stroke(51);

  input = createInput();
  inputone = createInput();
  inputtwo = createInput();
  input.position(20, 65);
  inputone.position(160,65);
  inputtwo.position(300,65);

  button = createButton('submit');
  button.position(440, 65);
  button.mousePressed(greet);

  greeting = createElement('h4', 'Remote Port');
  greetingone = createElement('h4', 'Local Port');
  greetingtwo = createElement('h4', 'IP Address');
  greeting.position(20, 5);
  greetingone.position(160,5);
  greetingtwo.position(300,5);

  textAlign(CENTER);
  textSize(50);
}


function greet() {

  REMOTE_PORT = input.value();
  LOCAL_PORT = inputone.value();
  IPADDRESS = inputtwo.value();
  input.value('');
  inputone.value('');
  inputtwo.value('');

  if(IPADDRESS == undefined){
    IPADDRESS == '127.0.0.1';
  }
  // console.log(REMOTE_PORT);
  // console.log(LOCAL_PORT);
  client = new osc.Client(IPADDRESS, REMOTE_PORT);
  oscServer = new osc.Server(LOCAL_PORT, '0.0.0.0');

  oscServer.on("message", function (msg, rinfo) {

  if(msg[0] == "/brush-x"){
    x = msg[1];
    if((y!=undefined)&&(z!=undefined)){
       ellipse(x,y,10);
       stroke(z,random(z),random(255-z));
       x=undefined;
       y=undefined;
       z=undefined;
    }
  }

  else if (msg[0] == "/brush-y") {
    y = msg[1];
    if((x!=undefined)&&(z!=undefined)){
       ellipse(x,y,10);
       stroke(z,random(z),random(255-z));
       x=undefined;
       y=undefined;
       z=undefined;
    }
  }

  else if(msg[0] == "/brush-z"){
    z = msg[1];
    if((x!=undefined)&&(y!=undefined)){
       ellipse(x,y,3);
       stroke(z,random(z),random(255-z));
       x=undefined;
       y=undefined;
       z=undefined;
    }
  }

});

}

// console.log(REMOTE_PORT);
// console.log(LOCAL_PORT);

function mouseDragged() {

	line(mouseX, mouseY, pmouseX, pmouseY);
  colorchange = int(random(255));

	client.send('/brush-y', mouseY);
  client.send('/brush-x', mouseX);
  client.send('/brush-z', colorchange);
}




