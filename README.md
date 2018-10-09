<h1> Tutorial - TensorFlow Object Detection API Tutorial (Ubuntu e derivados) </h1>

Data de criação: 09/10/2018
<br>Versão do SO: Ubuntu 16.04.5 LTS
<br> Versão do TensorFlow: 1.11.0

Este tutorial tem como objetivos de mostrar como configurar o <i>TensorFlow Object Detection API</i> para utilizá-lo com um <i>dataset</i> próprio.

<h2>1. Criando um Ambiente Virtual</h2>
Para evitar qualquer problema de incompatibilidade com os pacotes globalmente instalados, será criado um ambiente virtual para isolar a instalação da API dos restante dos pacotes instalados no sistema operacional. Inicialmente, podemos verificar se existe os seguintes componentes estão instalados:

<code>$ python3 --version</code><br>
<code>$ pip3 --version</code><br>
<code>$ virtualenv --version</code><br>

Caso todos estes pacotes já estejam instalados, pule o próximo passo:

<code>$ sudo apt update</code><br>
<code>$ sudo apt install python3-dev python3-pip</code><br>
<code>$ sudo pip3 install -U virtualenv</code><br>

Agora, crie uma pasta no local desejado (pode ser na Home). O ambiente virtual estará contido dentro da mesma. Por exemplo:<br>
<code>$ mkdir venv </code><br>

O comando anterior cria uma pasta chamada de <i>venv</i>. Agora, execute o seguinte comando para criar o ambiente virtual:
<br><code>$ virtualenv --system-site-packages -p python3 <b>venv</b></code><br> 

Caso seja necessário, troque a palavra <i>venv</i> pelo nome da pasta que você criou.

Assim o seu ambiente virtual está criado. Para ativá-lo, digite o seguinte comando: <br>
<code>$ source venv/bin/activate </code><br>

Se tudo ocorrer conforme o esperado, o nome do seu ambiente virtual aparecerá, entre parênteses, no seu terminal.
<code>(venv) $ </code><br>

Para desativá-lo, digite o seguinte comando: <br>
<code>(venv) $ deactivate</code>

<h2>2. Instalando o TensorFlow</h2>
Dentro do ambiente virtual, digite os seguinte comandos:
<code>(venv) $ pip install --upgrade pip</code><br>
<code>(venv) $ pip list</code><br>

O primeiro atualiza o <i>pip</i> dentro do ambiente virtual, e o segundo, se tudo ocorrer como esperado, mostra os pacotes instalados. 

Existem duas opções para a instalação do TensorFlow: com e sem suporte ao processamento em GPU. O suporte para placas de vídeos funciona para aquelas que possuem um <i>CUDA Capability</i> superior a 3.5. Para saber mais e verificar o valor para sua GPU, acesse: https://developer.nvidia.com/cuda-gpus. Caso a sua placa de vídeo possua <i>CUDA Capability</i> menor que 3.5, existe a necessidade de compilar o TensorFlow a partir do seu código fonte (https://www.tensorflow.org/install/source). Ainda, é necessário que os <i>drivers</i> da GPU, CUDA e cuDNN estejam devidamente instalados. Aqui, serão utilizados pacotes pré-compilados.

TensorFlow CPU: <code>(venv) $ pip install --upgrade tensorflow</code><br>
TensorFlow GPU: <code>(venv) $ pip install --upgrade tensorflow-gpu</code><br>

Para checar se a instalação está funcionando corretamente:

<code>(venv) $ python -c "import tensorflow as tf; print(tf.\_\_version\_\_)"</code>

<h2>3. Baixando e instalando o TensorFlow Object Detection API</h2>
Inicialmente, é necessário baixar os arquivos para posterior instalação da API. Para isso, acesse o link: https://github.com/tensorflow/models. Para este tutorial, foi utilizado <a href="https://github.com/tensorflow/models/tree/42f98218d7b0ee54077d4e07658442bc7ae0e661">este</a> commit. Faça o download do repositório clicando no botão verde, no canto superior direito (<i>Clone or Download</i>). Após o arquivo ter sido baixado, extraia e renomeie a pasta "models-master" para "models". No terminal: 
<code>mv models-master models</code><br>
 
Agora, é necessário mover a pasta <i>models</i> para dentro da pasta <i>venv</i>. Você pode simplesmente copiar e colar ou utilizar o terminal:
<code>mv master <b>/home/user/venv</b></code> (Substituir o caminho em negrito pelo caminho em seu computador.)<br>

<h3>3.1 Instalando a API</h3>

Inicialmente, é necessário instalar as dependências necessárias para o funcionamento da API. Para isto, digite:
<br><code>(venv) $ pip install --user Cython</code><br>
<code>(venv) $ pip install --user contextlib2</code><br>
<code>(venv) $ pip install --user pillow</code><br>
<code>(venv) $ pip install --user lxml</code><br>
<code>(venv) $ pip install --user matplotlib</code><br>

<h4>3.1.1 Instalando a COCO API</h4>
Caso você esteja interessdo em utilizar as métricas COCO, é necessário instalar a sua API. Para isto, digite:
<code>(venv) $ git clone https://github.com/cocodataset/cocoapi.git</code>
<code>(venv) $ cd cocoapi/PythonAPI</code>
<code>(venv) $ make</code>
<code>(venv) $ cp -r pycocotools <path_to_tensorflow>/models/research/</code>
  
<h4>3.1.2 Instalando o protobuf-compiler</h4>
Dentro da pasta <i>venv/models/research</i>, digite os seguintes comandos:  
<code>(venv) $ wget -O protobuf.zip https://github.com/google/protobuf/releases/download/v3.0.0/protoc-3.0.0-linux-x86_64.zip</code>
<code>(venv) $ unzip protobuf.zip</code>

Agora que os arquivos foram extraídos, ainda dentro de <i>venv/models/research</i>, digite o comando:
<br><code>./bin/protoc object_detection/protos/*.proto --python_out=.</code>

<h4>3.1.3 Adicionando a API ao <i>path</i> do sistema</h4>
