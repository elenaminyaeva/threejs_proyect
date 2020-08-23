# threejs_proyect
## THREE JS

**Inicio**
```
var scene = new THREE.Scene();
var camera = new THREE.OrthographicCamera (sceneSize.x / -2, sceneSize.x / 2, sceneSize.y / 2, sceneSize.y / -2, 0, 1000);
```

En este modo de proyección (proyección paralela), el tamaño de un objeto en la imagen renderizada permanece constante independientemente de su distancia de la cámara.

```
function render() {
            ...
            requestAnimationFrame(render);
            renderer.render(scene, camera);

        }
```

Funcción **render** creará un bucle que hará que el renderizador dibuje la escena cada vez que se actualice la pantalla. Reaciona a todos los eventos del programa (timer, mouse click etc.)

Definir el tamaño del render: 
```
var sceneSize = { 
    x: 1920,
    y: 730,
    ratio: 0.0
}
```
Definir el tamaño de la pantalla:

```
var rendererSize = { 
    x: window.innerWidth,
    y: window.innerWidth / sceneSize.ratio
}
```

El tamaño de la pantalla se calcula en dependencia del tamaño de la escena para no deformar el imagen.


**Creación del objeto**

+ Geometría

```
        var circleGeometry = new THREE.CircleGeometry(50, 30);
        var cubeGeometry = new THREE.BoxGeometry(80, 80, 80);
        var triangleGeometry = new THREE.CircleGeometry(60, 3);
        var squareGeometry = new THREE.Geometry();
        squareGeometry.vertices.push(new THREE.Vector3(-50, -50, 0));
        squareGeometry.vertices.push(new THREE.Vector3(-50, 50, 0));
        squareGeometry.vertices.push(new THREE.Vector3(50, 50, 0));
        squareGeometry.vertices.push(new THREE.Vector3(50, -50, 0));
        squareGeometry.faces.push(new THREE.Face3(0, 3, 2));
        squareGeometry.faces.push(new THREE.Face3(0, 2, 1));
        var rhombusGeometry = new THREE.Geometry();
        rhombusGeometry.vertices.push(new THREE.Vector3(0, -70, 0));
        rhombusGeometry.vertices.push(new THREE.Vector3(-40, 0, 0));
        rhombusGeometry.vertices.push(new THREE.Vector3(0, 70, 0));
        rhombusGeometry.vertices.push(new THREE.Vector3(40, 0, 0));
        rhombusGeometry.faces.push(new THREE.Face3(0, 3, 2));
        rhombusGeometry.faces.push(new THREE.Face3(0, 2, 1));

```

*Note: no hay figuras 2D estándares cuadrado y rombo en biblioteca de NodeJS*

+ Mesh 

Clase que representa objetos basados en malla poligonal triangular.

Constructor --> Mesh( geometry : Geometry, material : Material )

```
obj[i].mesh = new THREE.Mesh(squareGeometry, new THREE.MeshBasicMaterial({ 
    color: matherials[obj[i].color]
}));
```

Cada objeto tipo *Mesh* tiene sus propiedades como *posición*, *rotación* (coordinadas x,y,z)

```
obj[i].mesh.rotation.x = Math.PI
```

Hay varios tipos de materiales, en nuestro caso utilizamos MeshBasicMaterial y MeshPhongMaterial. La difirencia principal es la sensibilidad a la luz. MeshBasicMaterial no se ve afectado por las luces y MeshPhongMaterial si. Para mostrar, por ejemplo, el cubo en proyección paralela necesitamos la luz para ver todos los bordes del cubo.

+ Fuente de luz

```
const lightColor = 0xffffff;
const lightIntensity = 1;
const Light = new THREE.DirectionalLight(lightColor, lightIntensity); 
Light.position.set(0, 0, 500);
Light.target.position.set(0, 0, -500);
scene.add(Light);
scene.add(Light.target);
```

## Tareas

+ Definición de matrices *figureForms, figureColors, matherials, figureMovement* en forma verbal

```
var figureForms = [
'square',
'circle',
'cube',
'triangle',
'rhombus'
];
```
+ Definición de la matríz en forma digital 

```
figures[i] = {
    x: 0.0,
    y: 0.0,
    form: 0,
    color: 0,
    movement: 0,
    speed: 0.0, // rotación, movimiento 
    angle: 0,
    material: 0,
    mesh: 0,
}
```
+ Generación de tareas

```
function generateTaskSet() {
    var completeTaskArray = [];
    var currentTask;

    for (var i = 0; i < numFigures; i++) completeTaskArray.push(i); 

    for (var i = 0; i < 5; i++) {

        currentTask = getRandomInt(0, numFigures - i - 1);

        taskSet[i] = {
            figureNumber: completeTaskArray[currentTask],
            complete: false
        };
        completeTaskArray.splice(currentTask, 1);


    }
}
```
La función generará las tareas para que no se repitan dentro del juego.
Se crea la matríz completa de todas las tareas posibles - completeTaskArray (depende de la cantidad de las figuras, colores etc.), escoge una variable aleatoria de la matríz - currentTask y la borra del completeTaskArray. 

+ Generación de figuras

```
function generateFigures(obj) {
```

De la misma forma se genera la matríz de lodas las posibles figuras con todas las posibles propiedades (geometría, color, tipo de movimiento). Nota: rotación del circulo excluida.

```
for (var i = 0; i < figureForms.length; i++) {
    for (var j = 0; j < figureColors.length; j++) {
        for (var k = 0; k < figureMovement.length; k++) {
            if (!(i == 1 && k == 3)) completeFiguresArray.push({
                f: i,
                c: j,
                m: k
            });
        }
    }
}
```

Posición de as figuras depende de la cantidad

```
if (numFigures <= 12) {
    xCord1 = 75 - (numFigures * 150 / 2);
    yCord = 0;
} else {
    xCord1 = 75 - (12 * 150 / 2);
    xCord2 = 75 - ((numFigures - 12) * 150 / 2);
    yCord = 175;
}
```

Si la cantidad es < 12, solo nesecitamos una fila, si > 12 --> 2 filas

```
xCord1 += 150;
if (xCord1 >= 960) xCord1 = xCord2;
```
Para mostrar figuras en fila cogemos la coordinada X (xCord1) de la primera figura y añadimos 150.

Luego escogemos figuras aleatorias de la matríz completeFiguresArray y las borramos como en la función anterior para no repetir. 

```
randomFigure = getRandomInt(0, completeFiguresArray.length - 1);
obj[i].form = completeFiguresArray[randomFigure].f;
obj[i].color = completeFiguresArray[randomFigure].c;
obj[i].movement = completeFiguresArray[randomFigure].m;
completeFiguresArray.splice(randomFigure, 1);
```
Cuando tenga las figuras eligidos de la matríz, el programa construye los objetos mesh definiendo el material y geometría depende del tipo de la figura.

```
case 0:
obj[i].mesh = new THREE.Mesh(squareGeometry, new THREE.MeshBasicMaterial({
color: matherials[obj[i].color]
}));
break;
```
*Nota: case number - el numero del elemento en la matríz, por ejemplo, case 0 = 'square' en la matríz figureForms*

De la misma forma se definen los movimientos:

```
case 1:
obj[i].y = getRandomInt(yCord, yCord + 100);
obj[i].speed = -1;
break;
```

Así la función generateFigures rellena la matríz *figures[i]*.
Añadir todos los mesh a la escena:

```
for (var i = 0; i < numFigures; i++) scene.add(figures[i].mesh);
```
+ Generación de la tarea (texto)

```
function generateTask() {
    var currentFigure = taskSet[currentRound].figureNumber; //getRandomInt(0, numFigures - 1);
    roundTask.form = figures[currentFigure].form;
    roundTask.color = figures[currentFigure].color;
    roundTask.movement = figures[currentFigure].movement;

    roundTime = maxRoundTime;
    gameTask.innerHTML = 'Find' + ' ' + figureMovement[roundTask.movement] + ' ' + figureColors[roundTask.color] + ' ' + figureForms[roundTask.form];
    var timerText = roundTime / 10;
    gameTimer.innerHTML = 'Time: ' + timerText.toFixed(1);
    gameTimer.style.color = '#00ff00';
    gameRoundInfo.innerHTML = 'Round: ' + (currentRound + 1) + '/5';
    gameScoreInfo.innerHTML = 'Score: ' + gameScore;

}
```

La función escoge la figura del TaskSet (*currentFigure*), anula el timer y actualiza las variables del texto que forman la frase de la tarea.

+ Complejidad

```
switch (gameDifficulty) {
    case 'Easy':
        numFigures = 12;
        maxRoundTime = 120.0;
        bonusRate = 20;
        break;
    case 'Medium':
        numFigures = 18;
        maxRoundTime = 80.0;
        bonusRate = 40;
        break;
    case 'Hard':
        numFigures = 24;
        maxRoundTime = 50.0;
        bonusRate = 100;
        break;

    default:
        numFigures = 18;
        maxRoundTime = 80.0;
        bonusRate = 40;
        break;
}
```

Depende del nivel de complejidad se definan la cantidad de las figuras y el tiempo del juego.
Según el nivel de complejidad se aumenta el precio de la bonificación.

+ Movimientos

La función se ejecuta con la función principal render ()

```
function render() {
    processFigures(figures);
    requestAnimationFrame(render);
    renderer.render(scene, camera);

}
```
```
function processFigures(obj) {
            var yCord;

            if (numFigures <= 12) yCord = 0;
            else yCord = 175;

            for (var i = 0; i < numFigures; i++) {
                if (i > 11) yCord = -175;

                switch (obj[i].movement) {
                    case 0:
                        break;
                    case 1:
                        obj[i].y += obj[i].speed;
                        //~ obj[i].y = Math.floor(obj[i].y);

                        obj[i].speed -= 0.5;
                        if (obj[i].y < (yCord - 100)) {
                            obj[i].speed = -(obj[i].speed + 0.5);
                        }
                        obj[i].mesh.position.y = obj[i].y;
                        break;
                    case 2:
                        obj[i].y = yCord + 100 + Math.sin(obj[i].angle) * 10;
                        obj[i].angle += 0.1;
                        obj[i].mesh.position.y = obj[i].y;
                        break;
                    case 3:
                        if (obj[i].form == 2) {
                            obj[i].mesh.rotation.x += 0.02 * obj[i].speed;
                            obj[i].mesh.rotation.y += 0.04 * obj[i].speed;
                            obj[i].mesh.rotation.x += 0.06 * obj[i].speed;
                        } else obj[i].mesh.rotation.z += 0.05 * obj[i].speed;
                        break;
                }
            }
        }
```

*Salto*/*jumping*
case 1: disminución de la velocidad del objeto con cada iteración del programa. Con cada iteración a la coordenada Y se añade el valor de la velocidad (el objeto "cae" con cada iteración más rapido). Cuando lluegue a 0 el valor de la velocidad cambia de signo (empieza a subir en vez de bajar), la velocidad se disminuye hasta que llegue a 0. Y así sucesivamente.

*Vuelo*/*flying*
case 2: también se cambia la ccordinada Y según la ley sinusoidal.

*Rotación*/*rotating*
case 3: si la figura no es el cubo la rotación se realiza por el angulo Z, si es el cubo la rotación se realiza por todos los angulos.


+ Definir el timer 

```
function roundTimer() {
    roundTime -= 1;
    var timerText = roundTime / 10;
    gameTimer.innerHTML = 'Time: ' + timerText.toFixed(1);
    if (roundTime <= 30) gameTimer.style.color = '#ff0000';
    else if (roundTime <= 60) gameTimer.style.color = '#ffff00';
    if (roundTime <= 0) {
        generateTask();
        currentRound++;
        if (currentRound <= 4) generateTask();
        else stopGame();
    }
}
```
El timer se muestra durante del juego, la función stopGame() lo borra.

```
 gameTimer.style.visibility = 'hidden';
```

## Out of tasks

+ Escalar imagen

```
function onWindowResize() {
            renderer.setPixelRatio(window.devicePixelRatio);
            rendererSize.x = window.innerWidth; // ancho
            rendererSize.y = window.innerWidth / sceneSize.ratio; // altura
            renderer.setSize(rendererSize.x, rendererSize.y);
            rescaleTextInfo(); // escalar el texto
        }
```
 
+ Mouse click

```
function getMousePosition(event) {

    var sMouseX = ((event.pageX) / rendererSize.x - 0.5) * sceneSize.x;
    var sMouseY = -((event.pageY - 100) / rendererSize.y - 0.5) * sceneSize.y;

    for (var i = 0; i < numFigures; i++) {
        if (sMouseX >= (figures[i].x - 80) && sMouseX <= (figures[i].x + 80) && sMouseY >= (figures[i].y - 80) && sMouseY <= (figures[i].y + 80)) {
            if (roundTask.form == figures[i].form && roundTask.color == figures[i].color && roundTask.movement == figures[i].movement) {
                timeScore += bonusRate * roundTime / maxRoundTime;
                taskSet[currentRound].complete = true;
            }

            if (currentRound < 4) {
                currentRound++;
                generateTask();
            } else stopGame();
        }

    }
}
```

Las coordinadas del ratón se representan con variables event.pageX, event.pageY. Las coordinadas dentro de la escena se calculan depende del renderSize.
El búcle comprueba la posición de cada figura en la escena y busca el cruce (las coordinadas del click +/- 80). Si hay un cruce la variable --> taskSet[currentRound].complete = true

Luego según el valor de la variable complete, se calculan los puntos de benificación.

+ Función stopGame()

La función quita el timer y el mouse click.
```
clearInterval(roundTimerId);
document.removeEventListener('click', getMousePosition);
```
Limpia la escena y dejar solo las figuras elegidas correctamente por el usuario.

```
for (var i = 0; i < numFigures; i++) {
    temp = false;
    for (var j = 0; j < 5; j++)
        if (taskSet[j].figureNumber == i) temp = true;
    if (!temp) scene.remove(figures[i].mesh);

}
```

Visualización de los resultados.

+ Función restartGame()

```
function restartGame() {

            for (var i = 0; i < numFigures; i++) {
                temp = false;
                for (var j = 0; j < 5; j++)
                    if (taskSet[j].figureNumber == i) temp = true;
                if (temp) scene.remove(figures[i].mesh);
            }

            gameScore = 0;
            timeScore = 0;
            currentRound = 0;
            gameTimer.style.color = '#00ff00';
            generateTaskSet();
            generateFigures(figures);
            for (var i = 0; i < numFigures; i++) scene.add(figures[i].mesh);
            generateTask();

            document.addEventListener('click', getMousePosition);
            roundTimerId = setInterval(roundTimer, 100);
            gameTimer.style.visibility = 'visible';
            restartButton.style.visibility = 'hidden';
            quitButton.style.visibility = 'hidden';

        }
```

La función limpia el resto de las figuras de la escena. 

+ Local Store

El almacén de los resultados se realiza con Local Store (como no hay base de datos connectada)

```
function getLocalStorageValue(str) {
            var tempStr = localStorage.getItem(str);
            var tempInt;
            if (tempStr == null) {
                totalScoreValue = '0';
                localStorage.setItem(str, totalScoreValue);
                tempStr = localStorage.getItem(str);
            }

            tempInt = parseInt(tempStr); // convierte string al numero 
            console.log(str + ' = ' + tempInt);
            return tempInt;
        }
```

## Demo

![DEMO](/demo/demo.mp4)
