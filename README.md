# lab-4-PDS - Señales electromiográficas EMG

## Descripción
Con el objetivo de aplicar el filtrado de señales contínuas para realizar el procesamiento de una señal electromiográfica y detectar la fatiga muscular a través del análisis espectral de la misma, se plantea el siguiente artículo, en el cual se muestra el paso a paso de la práctica de laboratorio, iniciando con el posicionamiento de los electrodos sobre el sujeto al que se le realizará la práctica, estos electrodos fueron conectados al dispositivo DAQ el cual cumple la función de adquirir los datos que posteriormente serán procesados por medio del computador, con el uso de la filtración estudiada en clase "pasa altas" "pasa bajas" y aventanamiento, con el fin de eliminar el ruido y preaparar la señal para el análisis espectral consiguiente; con el uso de la aplicación de la transformada rápida de fourier se realizó el análisis del espectro de frecuencias, concluyendo así que cuando se observa la disminución de la frecuencia será la evidencia de la fatiga muscular. 
## Desarrollo
### Adquisición de datos
La adquisición de la señal electromiográfica (EMG) se realizó mediante un sistema de captura conectado a electrodos de superficie, los cuales registraron la actividad eléctrica del músculo durante una contracción sostenida. Los datos adquiridos fueron almacenados en un archivo en formato CSV, conteniendo dos columnas: tiempo (en segundos) y voltaje (en voltios).

Para el procesamiento de la señal, se utilizó un script en Python, donde se llevó a cabo la carga y visualización de los datos registrados, primero se realizó la carga de y la visualización de la señal EMG implementada en el siguiente fragmento del código: 

     file_path = "emg_signal.csv"  
     df = pd.read_csv(file_path)
     tiempo = df.iloc[:, 0]   (Tiempo)
     voltaje = df.iloc[:, 1]   (Voltaje)

Con el fin de separar los datos del tiempo de los de voltaje. 

A continuación se muestra el fragmento del código con el que se realizó la estimación de la frecuencia de muestreo de la señal, tomando como base la diferencia entre las muestras consecutivas. 

     fs_estimates = 1 / tiempo.diff().dropna().unique()
     fs_mean = fs_estimates.mean()  
     print(f"Frecuencia de muestreo estimada: {fs_mean:.2f} Hz")

Para graficar el comportamiento de la señal se implementó el siguiente código, con el fin de determinar la variabilidad de la señal producto de la contracción:

     plt.figure(figsize=(10, 4))
     plt.plot(tiempo, voltaje, label="Señal EMG", color="b")
     plt.xlabel("Tiempo (s)")
     plt.ylabel("Voltaje (V)")
     plt.title("Señal EMG Original")
     plt.legend()
     plt.grid(True)
     plt.show()


### Filtros
### Aventananientos Hamming y transformada de fourier

## Ventana de Hamming
Con el objetivo de analizar la evolución espectral de la señal EMG a lo largo del tiempo y minimizar los efectos de las discontinuidades en los bordes de los segmentos, se aplicó una ventana de Hamming sobre segmentos de la señal. El aventanamiento consiste en dividir la señal en bloques de tamaño fijo y multiplicarlos por una función ventana que atenúa los extremos del bloque, evitando fugas espectrales.


     ventana = 2048  # Tamaño de la ventana
     hamming_window = np.hamming(ventana)

En donde se utilizó un tamaño de ventana de 2048 muestras y se aplicó la función np.hamming() para generar la ventana correspondiente. 

## Transformada de Fourier
Para estudiar la distribución de frecuencias y observar la aparición de fatiga muscular, se aplicó la Transformada Rápida de Fourier (FFT) a cada uno de los segmentos aventanados, para así poder obtener el espectro de amplitud de la señal EMG y calcular la frecuencia mediana, la cual tiende a disminuir progresivamente como signo de fatiga muscular.

     frecuencia_mediana = []
     for segmento in segmentos:
     fft_segmento = np.fft.fft(segmento)
     freqs = np.fft.fftfreq(len(segmento), d=1/fs_mean)
     magnitud = np.abs(fft_segmento)
     
     freqs_pos = freqs[freqs >= 0]
     magnitud_pos = magnitud[freqs >= 0]

     energia_total = np.sum(magnitud_pos)
     energia_acumulada = np.cumsum(magnitud_pos)
     idx_mediana = np.where(energia_acumulada >= energia_total / 2)[0][0]
     frecuencia_mediana.append(freqs_pos[idx_mediana])

En primer lugar se aplicó la función np.fft.fft() para transformar cada segmento al dominio de la frecuencia, luego, se realizó el cálculo de las frecuencias correspondientes y se filtraron las componentes positivas.

### Análisis espectral

El análisis espectral de la señal EMG tiene como finalidad identificar los cambios en la distribución de frecuencia de la señal a lo largo del tiempo, permitiendo evaluar la presencia de fatiga muscular.

Se puede iniciar destacando la frecuencia mediana la cual representa la frecuencia que divide el espectro de la señal en dos áreas de igual energía, de esta manera durante un esfuerzo sostenido, la frecuencia mediana tiende a desplazarse hacia valores más bajos debido a la disminución en la conducción de los potenciales de acción musculares, lo que es un indicativo de la aparición de fatiga.

Para cada segmento de la señal previamente aventanado con la ventana de Hamming, se calculó la FFT, obteniendo así el espectro de frecuencias correspondiente,  y puesto que la FFT produce un espectro simétrico, sólo se consideraron las frecuencias positivas para el análisis.

        fft_segmento = np.fft.fft(segmento)
        freqs = np.fft.fftfreq(len(segmento), d=1/fs_mean)
        magnitud = np.abs(fft_segmento)
   
## Creación de la hipótesis

     primera_contraccion = frecuencia_mediana[:5]
     ultima_contraccion = frecuencia_mediana[-5:]

     media1 = np.mean(primera_contraccion)
     media2 = np.mean(ultima_contraccion)
     s1 = np.std(primera_contraccion, ddof=1)
     s2 = np.std(ultima_contraccion, ddof=1)
     n1 = len(primera_contraccion)
     n2 = len(ultima_contraccion)

     t = (media1 - media2) / np.sqrt((s1**2 / n1) + (s2**2 / n2))

1. Para establecer la hipótesis se utilizaron las siguientes fórmulas:
   - H0: u1-u2=0
   - Ha: u1-u20
Por lo que se pudo determinar que no existe una diferencia importante entre la primera contracción y la última.

2. El estadístico de prueba se muestra implementado en el código anterior.
3. el valor crítico se aplicó manualmengte con  α = 0.05 y 4 grados de libertad, de manera que el tcrítico= 2,77
4. Se puede concluir que como ∣t∣=3.1256>2.776, es claramente rechazado, en el sentido de que hay una gran diferencia entre la primera y última contracción.




## Instrucciones
1) En primer lugar descargar o copiar y pegar el código de Python subido en este repositorio, el cuál esta guardado como "lab4.py", en el compilador que desee preferiblemente "Spyder". Cabe recalcar que debe asegurarse que se encuentren las librerias enlistadas en los requerimientos para que pueda compilarse adecuadamente el programa y muestre tanto las gráficas deseadas como los resultados de los cálculos. Adicionalmente como se esta trabajando con los DAQ debe de también tener instalado el "DAQ.mx" y su respectivo modulo en Python.

2) Para capturar la señal electromiográfica de su brazo, coloque dos electrodos en los músculos que desea analizar y otro en una protuberancia ósea que funcionara como tierra. Conecte los cables a los electrodos y al módulo ECG AD8232; alimentar a 3.3V, conectar a tierra y conectar la salida del modulo a una de las entradas del DAQ.

3) Conectar el DAQ al computador, debería de abrirse una ventana con el software del DAQ en el que se ajustan varios áspectos como la amplitud de los ejes y la frecuencia de muestreo. Cuando este todo preparado, dar click al boton de start y comenzar a hacer las contracciones continuas hasta llegar a ese momento de la fsatiga; mientras esto sucede se verá en la ventana del DAQ la señal a tiempo real. Por último dar click en stop cuando haya terminado.

4) 
   
5) Correr el programa, esperar a que se compile por completo y si todo está en orden, se verán los resultados en el terminal de comandos de la manera que se muestra en la siguiente figura con sus respectivas gráficas.

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
[1] Oppenheim, A. V., & Schafer, R. W. (2010). Discrete-Time Signal Processing (3rd ed.). Pearson.
[2] Smith, S. W. (1997). The Scientist and Engineer's Guide to Digital Signal Processing. California Technical Publishing.

## AUTORES
- Juan Diego Clavijo Fuentes
  est.juan.dclavijjo@unimilitar.edu.co
- Sofia Olivella Moreno
  est.sofia.olivella@unimilitar.edu.co

