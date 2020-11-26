const { append, cons, first, isEmpty, isList, length, rest } = functionalLight;

function make(data, attribute) {
  return Object.assign({}, data, attribute);
}
  
/**
 * Se definen los mundos
 * let, var, const namne = null; 
 * No requiere interacción con el usuario
*/
//const WIDTH = 400;
//const HEIGHT = 400;
const SIZE  = 25;
var posWater
var currentSecond = -1;
var secsDown = 5;
var map  = [[1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1,1],
            [1,3,2,2,2,2,2,2,2,2,1,2,1,2,1,2,1,2,1],
            [1,2,1,1,1,1,1,1,1,0,0,0,1,2,1,2,1,2,1],
            [1,2,1,2,2,1,2,2,1,0,0,0,0,0,1,2,1,2,1],
            [1,2,1,2,2,1,2,2,1,0,0,2,2,0,0,0,1,2,1],
            [1,1,1,2,2,1,2,2,1,0,0,1,1,0,0,0,0,2,1],
            [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,2,1],
            [1,0,0,0,1,1,1,1,1,1,1,1,2,2,1,2,1,2,1],
            [1,0,0,0,1,2,2,2,2,1,2,1,2,2,1,2,1,2,1],
            [1,0,0,0,1,2,2,2,2,1,2,1,2,2,1,2,1,2,1],
            [1,0,1,1,1,2,2,1,2,2,2,1,1,1,1,1,1,1,1],
            [1,0,1,2,2,2,2,1,2,2,2,2,2,2,2,2,2,2,1],
            [1,0,1,2,1,1,1,1,1,1,1,1,1,1,1,1,1,2,1],
            [1,0,1,2,1,2,2,2,2,2,2,2,2,2,2,2,1,2,1],
            [1,0,1,2,1,2,2,2,2,2,2,2,1,2,1,2,1,3,1],
            [1,0,2,2,2,2,2,1,2,2,2,1,1,1,1,1,1,1,1],
            [1,0,1,2,2,2,2,1,0,0,0,1,0,0,0,0,0,0,1],
            [1,0,1,1,1,1,1,1,0,0,0,1,0,0,0,0,0,0,1],
            [1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1],
            [1,1,1,1,1,1,1,1,0,0,0,1,1,1,1,1,1,1,1]];

const mapa = {
camino: 0,
moneda: 2,
muro: 1,
tomb: "p",
superMoneda: 3,
agua: "w"
}; 

//Esta es una funcion para calcular el numero total de monedas que se encuentran en el juego
//La funcion recorre las filas y columnas de la matriz, cuando termina de recorrer las columnas
function contarMonedasYSuperMonedas(matrix,x,y,contador){
  if(matrix && x < matrix.length){
    if(matrix[0] && y < matrix[0].length){
  if(matrix[x][y] == mapa.moneda || matrix[x][y]==mapa.superMoneda){
    contador++
  }
    return contarMonedasYSuperMonedas(matrix, x, y+1, contador);
    }
  else
    return contarMonedasYSuperMonedas(matrix, x+1, 0, contador);
  } else return contador;
}


function comerMonedas(world, x, y) {
  map[x][y] = mapa.camino
  return Object.assign(world,
  {tomb:{x:y, y:x}},
  { map },
  { puntaje: world.puntaje + 1 },
  { numeroDeMonedas: contarMonedasYSuperMonedas(map,0,0,0) }
  );
} 



function sketchProc(processing) {
  /**
   * Esto se llama antes de iniciar (espacio de trabajo)
  */
  processing.setup = function () {
     processing.frameRate(80);
     processing.size(470,500);
     processing.fill(180,130,20);
     tombImage = processing.loadImage("images/tomb.png");
     muroImage = processing.loadImage('images/MURON.jpg');
     monedaImage = processing.loadImage('images/coin.png');
     superMonedaImage = processing.loadImage('images/coin.png');
     aguaImage = processing.loadImage("images/water.jpg");
     sadGameOver = processing.loadImage("images/GAME.jpg");
     processing.state = 
     { time:0, tomb:{ x:9, y:17},map, inGame:true, currentTime:processing.second(), water:{time:0,speed:6,y:19,isKilling:true},
       numeroDeMonedas:contarMonedasYSuperMonedas(map,0,0,0), puntaje:0, vidas:3};
  }
    
  // Dibuja algo en el canvas. Aqui se pone todo lo que quieras pintar
  processing.drawGame = function (world) 
  {
    /*processing.background-image:url(images/"fondo.jpg");*/
    if(world.inGame){
    processing.background(0,0,0);

    //Vincula el elemento contarMonedas entre el HTML y el Script
    const selectionM = document.getElementById("numeroDeMonedas")
    selectionM.innerHTML = `${contarMonedasYSuperMonedas(map,0,0,0)}`

//Vincula el elemento world.puntaje entre el HTML y el Script
const selectionP = document.getElementById("puntaje")
selectionP.innerHTML = `${world.puntaje}`

/*const x = document.getElementById("time")
x.innerHTML = `${processing.second()}`*/

//Vincula el elemento world.vidas entre el HTML y el Script
/*const selectionV = document.getElementById("vidas")
selectionV.innerHTML = `${world.vidas}`*/


    function recursiveList(l,f,index=0){
      if(!isEmpty(l)){
        f(first(l),index);
        recursiveList(rest(l),f, index+1)
      }
    }    
    

    recursiveList(map,(row, i) => 
    {
      recursiveList(row,(cell, j)=> 
      {
        if(cell==1){//es para los muros del laberinto
          // nombre de imagen, tamaño columna, tamaño fila, ancho maximo, largo maximo.
          processing.image(muroImage, j * SIZE, i * SIZE, SIZE,SIZE); 
        }
        if (cell==2)
        { //este es para monedas 
          //tamaño de la columna, tomaño de la fila, ancho, largo
          processing.image(monedaImage, j * SIZE+10, i * SIZE+10, SIZE/2, SIZE/2);
        }
         if (cell==3){
          //Este es para las superMonedas
          processing.image(superMonedaImage, j * SIZE, i * SIZE, SIZE , SIZE);
      }
      });
    });

    //Este es para definir al tomb y que pueda aparecer en pantalla
    if (world.time == 0)
    {
     processing.image(tombImage,world.tomb.x * SIZE, world.tomb.y * SIZE, SIZE,SIZE);//con el processing.image, aparece la imagen que queremos que tenga nuestro tomb

    }

  
  
//Logica de eliminar al personaje con el agua
  if((Math.ceil(posWater / SIZE) > map[0].length - world.tomb.y)){
    
  //Logica para el movimiento del agua  
    world.inGame = false
  }
    
    if(currentSecond != processing.second()){
      if(processing.second() == 0){
      if(!world.water.isKilling)
      world.water.time -= 60;
      else
      world.water.time += 60;
      }
      if(!world.water.isKilling){
        posWater -= world.water.speed
        secsDown --
        if(secsDown == 0){
        world.water.isKilling=true;
         secsDown=5
         world.currentTime += 10
        }
      }
        else
        posWater = ((processing.second() - world.currentTime) + world.water.time)*world.water.speed
    
      currentSecond = processing.second();
    }
    

  //Este es para definir el agua y que pueda aparecer en pantalla
     processing.image(aguaImage,0, world.water.y * SIZE - posWater, SIZE*20,SIZE*19);//Con el processin.image, aparece la imagen que queremos que tenga nuestra agua
    }
    //cuando ya se termina el juego
  else processing.image(sadGameOver, 0, 0, 470,500);
  }



  // Actualiza el mundo despues de cada frame. En este ejemplo, no cambia nada, solo retorna una copia del mundo
  processing.onTic = function (world) 
  {
    return make(world, {});
  }

    //Implemente esta función si quiere que su programa reaccione a eventos del mouse
  processing.onMouseEvent = function (world, event) {
    // Por ahora no cambia el mundo. Solo retorna una copia del mundo actual
    return make(world, {});
  };

    //Implemente esta función si quiere que su programa reaccione a eventos del teclado
  processing.onKeyEvent = function (world, keycode)   
  {
      //keycode
    if (keycode == processing.LEFT){
      var valorPosicion=world.map[world.tomb.y][world.tomb.x-1]
      if (valorPosicion!= mapa.muro){
        if(valorPosicion==mapa.moneda || valorPosicion==mapa.superMoneda){
          if(valorPosicion==mapa.superMoneda){
          world.water.isKilling = false
          }
          return comerMonedas(world, world.tomb.y, world.tomb.x-1);
        }
      return make(world, {tomb:{x:world.tomb.x-1, y:world.tomb.y}});
      }
    }
   
    else if(keycode == processing.RIGHT){
      var valorPosicion=world.map[world.tomb.y][world.tomb.x+1]
      if(valorPosicion != mapa.muro){
        if(valorPosicion==mapa.moneda || valorPosicion==mapa.superMoneda){
          if(valorPosicion==mapa.superMoneda)
          world.water.isKilling = false
         return comerMonedas(world, world.tomb.y, world.tomb.x+1)
        }
       return make(world, {tomb:{x:world.tomb.x+1, y:world.tomb.y}});  
      }
    }
    else if(keycode == processing.DOWN){
      if(world.map[0].length >= world.tomb.y+1){
      var valorPosicion=world.map[world.tomb.y+1][world.tomb.x]
      if( valorPosicion != mapa.muro){
       if(valorPosicion==mapa.moneda || valorPosicion==mapa.superMoneda){
         if(valorPosicion==mapa.superMoneda)
          world.water.isKilling = false
        return comerMonedas(world, world.tomb.y+1, world.tomb.x)
       }
       return make(world, {tomb:{x:world.tomb.x, y:world.tomb.y+1}});
      }   
    }
  }
  
    else if(keycode == processing.UP){
      var valorPosicion=world.map[world.tomb.y-1][world.tomb.x]
      if(valorPosicion != mapa.muro){
        if(valorPosicion==mapa.moneda || valorPosicion==mapa.superMoneda){
          if(valorPosicion==mapa.superMoneda){
          world.water.isKilling = false
        }
         return comerMonedas(world, world.tomb.y-1, world.tomb.x)
        }
        return make(world, {tomb:{x:world.tomb.x, y:world.tomb.y-1}});
      }  
    }
      return make(world, {});
      
      
  }
  

    // ******************** De aquí hacia abajo no debe cambiar nada. ********************

    // Esta es la función que pinta todo. Se ejecuta n veces por segundo. 
    // No cambie esta función. Su código debe ir en drawGame
    processing.draw = function () {
      processing.drawGame(processing.state);
      processing.state = processing.onTic(processing.state);
    };

    // Esta función se ejecuta cada vez que presionamos una tecla. 
    // No cambie esta función. Su código debe ir en onKeyEvent
    processing.keyPressed = function () {
      processing.state = processing.onKeyEvent(processing.state, processing.keyCode);
    }

    // Esta función se ejecuta cada vez movemos el mouse. 
    // No cambie esta función. Su código debe ir en onKeyEvent
    processing.mouseMoved = function () {
      processing.state = processing.onMouseEvent(processing.state,
        { action: "move", mouseX: processing.mouseX, mouseY: processing.mouseY });
    }

    // Estas funciones controlan los eventos del mouse. 
    // No cambie estas funciones. Su código debe ir en OnMouseEvent
    processing.mouseClicked = function () {
      processing.state = processing.onMouseEvent(processing.state,
        { action: "click", mouseX: processing.mouseX, mouseY: processing.mouseY, mouseButton: processing.mouseButton });
    }

    processing.mouseDragged = function () {
      processing.state = processing.onMouseEvent(processing.state,
        { action: "drag", mouseX: processing.mouseX, mouseY: processing.mouseY, mouseButton: processing.mouseButton });
    }

    processing.mousePressed = function () {
      processing.state = processing.onMouseEvent(processing.state,
        { action: "press", mouseX: processing.mouseX, mouseY: processing.mouseY, mouseButton: processing.mouseButton });
    }

    processing.mouseReleased = function () {
      processing.state = processing.onMouseEvent(processing.state,
        { action: "release", mouseX: processing.mouseX, mouseY: processing.mouseY, mouseButton: processing.mouseButton });
    }
    // Fin de los eventos del mouse
}

  var canvas = document.getElementById("canvas");

  // Adjuntamos nuestro sketch al framework de processing
  var processingInstance = new Processing(canvas, sketchProc);
