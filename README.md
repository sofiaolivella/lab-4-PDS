# lab-4-PDS - Señales electromiográficas EMG

## Descripción

## Desarrollo
### Adquisición de datos
### Filtros
### Aventananientos Hamming y transformada de fourier
### Análsis espectral

## Instrucciones
1) En primer lugar descargar o copiar y pegar el código de Python subido en este repositorio, el cuál esta guardado como "lab4.py", en el compilador que desee preferiblemente "Spyder". Cabe recalcar que debe asegurarse que se encuentren las librerias enlistadas en los requerimientos para que pueda compilarse adecuadamente el programa y muestre tanto las gráficas deseadas como los resultados de los cálculos. Adicionalmente como se esta trabajando con los DAQ debe de también tener instalado el "DAQ.mx" y su respectivo modulo en Python.

2) Para capturar la señal electromiográfica de su brazo, coloque dos electrodos en los músculos que desea analizar y otro en una protuberancia ósea que funcionara como tierra. Conecte los cables a los electrodos y al módulo ECG AD8232; alimentar a 3.3V, conectar a tierra y conectar la salida del modulo a una de las entradas del DAQ.

3) Conectar el DAQ al computador, debería de abrirse una ventana con el software del DAQ en el que se ajustan varios áspectos como la amplitud de los ejes y la frecuencia de muestreo. Cuando este todo preparado, dar click al boton de start y comenzar a hacer las contracciones continuas hasta llegar a ese momento de la fsatiga; mientras esto sucede se verá en la ventana del DAQ la señal a tiempo real. Por último dar click en stop cuando haya terminado.

4) NO SE LO DE MATLAB
   
6) Correr el programa, esperar a que se compile por completo y si todo está en orden, se verán los resultados en el terminal de comandos de la manera que se muestra en la siguiente figura con sus respectivas gráficas.

## REQUERIMIENTOS
- DAQ
- Modulo ECG AD8232 con su respectivo cable y electrodos
- Gel conductora
- Python 3.11
- Spyder 6.0
- NI-DAQmx
- Matlab
- Librerias como: wfdb, matplotlib, numpy, scipy.io.wavfile, sklearn, sounddevice

## REFERENCIAS
[1] 
[2] 

## AUTORES
- Juan Diego Clavijo Fuentes
  est.juan.dclavijjo@unimilitar.edu.co
- Sofia Olivella Moreno
  est.sofia.olivella@unimilitar.edu.co

