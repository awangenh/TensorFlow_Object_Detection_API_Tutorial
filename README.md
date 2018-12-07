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
<br><code>(venv) $ ./bin/protoc object_detection/protos/*.proto --python_out=.</code>

<h4>3.1.3 Adicionando a API ao <i>path</i> do sistema</h4>
Para que o Python encontre os arquivos da API, é necessário adicioná-los ao <i>path</i> do sistema. No terminal, digite:

<code>vim ~/.bashrc</code>

Caso não queira utilizar o <i>vim</i>, outros editores de texto como o <i>nano</i> podem ser utilizados. No fim do arquivo, o seguinte comando deve ser utilizado:

<code>export PYTHONPATH=$PYTHONPATH:'pwd'/models/research/:'pwd'/models/research/slim</code>

O comando 'pwd' deve ser substituído pelo caminho até a pasta da API. Então, um exemplo de utilização seria:

<code>export PYTHONPATH=$PYTHONPATH:/home/user/od_api/models/research/:/home/user/od_api/models/research/slim</code>

Inserido o comando, pode-se salvar e fechar o arquivo. Para que o sistema seja atualizado e detecte a API, digite o seguinte comando no terminal: 

<code>source ~/.bashrc</code>

Para testar a instalação da API, dentro da pasta <i>venv/models/research</i> digite:

<code>(venv) $ python object_detection/builders/model_builder_test.py</code>

Uma mensagem como a abaixo deve aparecer se tudo estiver instalado da maneira correta:

<i>Ran 21 tests in 0.178s <br>

OK</i>

<h2>4. Gerando os dados para o treinamento</h2>
<h3>4.1 Organizando as pastas</h3>
A partir de agora vamos considerar a pasta <i>venv/models/research/object_detection</i> como a nossa pasta raíz. Para facilitar o manuseio dos arquivos, sugiro criar algumas pastas dentro da mesma. São elas:

<b>images</b>: local para salvar as imagens de treino e teste;<br>
<b>inference_graph</b>: onde ficará o grafo de inferência, após o modelo ser treinado;<br>
<b>training</b>: pasta onde o TensorFlow salvará todos os dados relacionados ao treinamento da rede.<br>

<h3> 4.2 Marcando as imagens e criando amostras para treinamento</h3>
Para a marcação das imagens, recomenda-se o <i>software</i> <a href="https://github.com/tzutalin/labelImg"> LabelImg</a>. Como saída para cada imagem, o programa gera um XML contendo as informações dos <i>bounding boxes</i> criados. Antes de gerarmos os TFRecords (arquivo utilizado como entrada da rede), é necessário converter estes XMLs para CSVs. Para isso, crie uma pasta chamada "train" e cole todas as imagens de treino, assim como os seus respectivos XMLs, dentro deste. Para o teste, crie uma pasta chamada "test" e faça o mesmo procedimento. Mova ambas pastas para a "images", previamente criada. Feito isso, utilize o arquivo "xml_to_csv.py" encontrado neste repositório, e na pasta <i>object_detection</i> execute o comando:

<code> (venv) $ python xml_to_csv.py <br>

Se tudo acontecer de maneira correta, as mensagens abaixo devem aparecer:
<br><br><i>Successfully converted xml to csv.<br>
Successfully converted xml to csv.</i>

Este processo criará os arquivos "train_labels.csv" e "test_labels.csv" na pasta "images". Agora, dentro da pasta "training" é necessário criar um arquivo com o nome "label_map.pbtxt". Dentro deste deve conter apenas o seguinte conteúdo:

<code>
item { <br>
  id: 1 <br>
  name: 'class' <br>
} <br>
</code>

Substitua "class" pela classe desejada. Se houver mais de uma classe, deve-se adicionar abaixo deste um novo trecho alterando o "id" e o "name". Por exemplo:

<code>
item {
  id: 1
  name: 'class1'
}
 
item {
  id: 2
  name: 'class2'
}
</code>

E assim por diante. Agora é necessário criar os TFRecords. Para isso, utilize o script "generate_tfrecord.py", encontrado neste repositório. Antes de executá-lo, é necessário alterar o seguinte trecho do código (linha 30):

<code>
# TO-DO replace this with label map <br>
def class_text_to_int(row_label): <br>
    if row_label == 'class': <br>
        return 1 <br>
    else: <br>
        None <br>
</code>

Este trecho deve ser alterado de acordo com que foi criado o label map. Ou seja, se houverem mais classes, deve-se seguir o exemplo abaixo:

<code>
# TO-DO replace this with label map <br>
def class_text_to_int(row_label): <br>
    if row_label == 'class1': <br>
        return 1 <br>
    elif row_label == 'class2': <br>
        return 2 <br>
    else: <br>
        None <br>
</code>

É <b>necessário</b> que as classes estejam na mesma ordem e com o mesmo <i>id</i> que foram colocados no arquivo <i>label_map.pbtxt</i>.

<h3> 4.3 Ajustando os arquivos necessários </h3>
O processo de treinamento será iniciado utilizando os pesos de modelos previamente treinados e fornecidos pela própria Google. Para isso, é necessário entrar no <a href="https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md">Model Zoo</a> e baixar o modelo <i>faster_rcnn_resnet101_coco</i> (ou baixá-lo diretamente por este <a href="http://download.tensorflow.org/models/object_detection/faster_rcnn_resnet101_coco_2018_01_28.tar.gz">link</a>). Feito isso, descompacte o arquivo baixado e mova a pasta <i>faster_rcnn_resnet101_coco_2018_01_28</i> para a pasta raíz (<i>object_detection</i>).

Agora, a partir da pasta raíz é necessário navegar até <i>samples/configs</i> e copiar o arquivo <i>faster_rcnn_inception_v2_coco.config</i> (modelo de rede utilizada) para dentro da pasta "object_detection/training". Dentro do arquivo será necessário editar algumas linhas. Altere os valores como no exemplo abaixo:
<code>
 Linha 10 (Quantidade de classes) -> num_classes: 1
 
 Linha 13 (Dimensão mínima das imagens de entrada) -> min_dimension: 512
 
 Linha 14 (Dimensão máxima das imagens de entrada) -> max_dimension: 512
 
 Linha 107 (Local de onde serão carregados os pesos iniciais)-> fine_tune_checkpoint: "faster_rcnn_inception_v2_coco_2018_01_28/model.ckpt"
 
 Linha 113 (Quantidade de passos do treinamento) -> num_steps: 50000
 
 Linha 122 (Local onde estão os TFRecords relacionados ao treinamento) -> input_path: "training/train.record"
 
 Linha 124 (Local onde está o <i>label map</i> relacionado ao treinamento) -> label_map_path: "training/label_map.pbtxt"
 
 Linha 136 (Local onde estão os TFRecords relacionados ao teste) -> input_path: "training/test.record"
 
 Linha 138 (Local onde está o <i>label map</i> relacionado ao test) -> label_map_path: "training/label_map.pbtxt"
 
</code>

Feitas as alterações, basta salvar e fechar o arquivo.

<h3> 4.4 Treinando a rede </h3>
