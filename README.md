<h1> Tutorial - Instalação da <i>TensorFlow Object Detection API</i> em Ubuntu e derivados </h1>

<br>Versão do SO: Ubuntu 16.04.5 LTS
<br> Versão do TensorFlow: 1.11.0

Este tutorial tem como objetivo mostrar como configurar o <i>TensorFlow Object Detection API</i> para utilizá-lo com um <i>dataset</i> próprio, em sistemas operacionais baseados em Ubuntu. Caso você use Windows, dê uma olhada no excelente <a href="https://github.com/EdjeElectronics/TensorFlow-Object-Detection-API-Tutorial-Train-Multiple-Objects-Windows-10"> tutorial </a> criado por <a href="https://github.com/EdjeElectronics"> EdjeElectronics</a>, no qual este tutorial foi baseado. Ainda, os <i>scripts</i> disponibilizados aqui foram retirados do tutorial citado anteriormente, com algumas pequenas mudanças para executar da maneira correta no Linux.

<h2>1. Criando um Ambiente Virtual</h2>

Para evitar qualquer problema de incompatibilidade com os pacotes globalmente instalados, será criado um ambiente virtual. Assim é possível isolar a instalação da API do restante dos pacotes instalados no sistema operacional. Inicialmente, podemos verificar se os seguintes componentes estão instalados:

<code>$ python3 --version</code><br>
<code>$ pip3 --version</code><br>
<code>$ virtualenv --version</code>

Caso todos estes pacotes já estejam instalados, pule o próximo passo:

<code>$ sudo apt update</code><br>
<code>$ sudo apt install python3-dev python3-pip</code><br>
<code>$ sudo pip3 install -U virtualenv</code>

Agora, crie uma pasta no local desejado. O ambiente virtual estará contido dentro da mesma. Por exemplo:

<code>$ mkdir venv </code><br>

O comando anterior cria uma pasta chamada de <i>venv</i>. Agora, execute o seguinte comando para criar o ambiente virtual:

<code>$ virtualenv --system-site-packages -p python3 <b>venv</b></code><br> 

Caso seja necessário, troque a palavra <i>venv</i> pelo nome da pasta que você criou.

O seu ambiente virtual está criado. Para ativá-lo, digite o seguinte comando: 

<code>$ source venv/bin/activate </code><br>

Se tudo ocorrer conforme esperado, o nome do seu ambiente virtual aparecerá, entre parênteses, no seu terminal.

<code>(venv) $ </code>

Para desativá-lo, digite o seguinte comando: 

<code>(venv) $ deactivate</code>

<h2>2. Instalando o TensorFlow</h2>

Com o ambiente virtual ativado, digite os seguinte comandos:

<code>(venv) $ pip install --upgrade pip</code><br>
<code>(venv) $ pip list</code><br>

O primeiro atualiza o <i>pip</i> dentro do ambiente virtual, e o segundo, se tudo ocorrer como esperado, mostra os pacotes instalados. 

Existem duas opções para a instalação do TensorFlow: com e sem suporte ao processamento em GPU. O suporte para placas de vídeos funciona para aquelas que possuem um <i>CUDA Capability</i> superior a 3.5. Para saber mais e verificar o valor para sua GPU, acesse: https://developer.nvidia.com/cuda-gpus. Caso a sua placa de vídeo possua <i>CUDA Capability</i> menor que 3.5, existe a necessidade de compilar o TensorFlow a partir do código fonte (https://www.tensorflow.org/install/source). Ainda, é necessário que os <i>drivers</i> da GPU, CUDA e cuDNN estejam devidamente instalados. Aqui, serão utilizados pacotes pré-compilados, sem suporte a GPU.

TensorFlow CPU: <code>(venv) $ pip install --upgrade tensorflow</code><br>
TensorFlow GPU: <code>(venv) $ pip install --upgrade tensorflow-gpu</code><br>

Para checar se a instalação está funcionando corretamente utilize:

<code>(venv) $ python -c "import tensorflow as tf; print(tf.\_\_version\_\_)"</code>

Se estiver corretamente instalado, a versão do pacote será impressa na tela.

<h2>3. Baixando e instalando o TensorFlow Object Detection API</h2>
Inicialmente, é necessário baixar os arquivos para posterior instalação da API. Para isso, acesse o link: https://github.com/tensorflow/models. Para este tutorial, foi utilizado <a href="https://github.com/tensorflow/models/tree/42f98218d7b0ee54077d4e07658442bc7ae0e661">este</a> commit. Faça o download do repositório clicando no botão verde, no canto superior direito (<i>Clone or Download</i>). Após o arquivo ter sido baixado, extraia e renomeie a pasta "models-master" para "models". No terminal: <code>mv models-master models</code>
 
<br>Agora, é necessário mover a pasta <i>models</i> para dentro da pasta <i>venv</i>. Você pode simplesmente copiar e colar ou utilizar o terminal:
<code>mv models <b>/home/user/venv</b></code> (Substituir o caminho em negrito pelo caminho em seu computador.)<br>

<h3>3.1 Instalando a API</h3>

Inicialmente, é preciso instalar as dependências necessárias para o funcionamento da API. Para isto, digite:

<code>(venv) $ pip install --user Cython</code><br>
<code>(venv) $ pip install --user contextlib2</code><br>
<code>(venv) $ pip install --user pillow</code><br>
<code>(venv) $ pip install --user lxml</code><br>
<code>(venv) $ pip install --user matplotlib</code><br>

<h4>3.1.1 Instalando a COCO API</h4>

Caso você esteja interessdo em utilizar as métricas COCO (recomendado), é necessário instalar a sua API. Para isto, digite:

<code>(venv) $ git clone https://github.com/cocodataset/cocoapi.git</code><br>
<code>(venv) $ cd cocoapi/PythonAPI</code><br>
<code>(venv) $ make</code><br>
<code>(venv) $ cp -r pycocotools <b>/home/user/venv/models/research/</b></code><br>

Substitua o caminho em negrito pela localização em seu computador.

<h4>3.1.2 Instalando o protobuf-compiler</h4>

Dentro da pasta <i>venv/models/research</i>, digite os seguintes comandos:  

<code>(venv) $ wget -O protobuf.zip https://github.com/google/protobuf/releases/download/v3.0.0/protoc-3.0.0-linux-x86_64.zip</code><br>
<code>(venv) $ unzip protobuf.zip</code>

Agora que os arquivos foram extraídos, ainda dentro de <i>venv/models/research</i>, digite o comando:

<code>(venv) $ ./bin/protoc object_detection/protos/*.proto --python_out=.</code>

Se tudo ocorreu como esperado, nenhuma mensagem será mostrada na tela.

<h4>3.1.3 Adicionando a API ao <i>path</i> do sistema</h4>

Para que o Python encontre os arquivos da API, é necessário adicioná-los ao <i>path</i> do sistema. No terminal, digite:

<code>vim ~/.bashrc</code>

Caso não queira utilizar o <i>vim</i>, outros editores de texto como o <i>nano</i> podem ser utilizados. No fim do arquivo, cole o seguinte comando:

<code>export PYTHONPATH=$PYTHONPATH:'pwd'/models/research/:'pwd'/models/research/slim</code>

O comando 'pwd' deve ser substituído pelo caminho até a pasta da API. Então, um exemplo de utilização seria:

<code>export PYTHONPATH=$PYTHONPATH:/home/user/venv/models/research/:/home/user/venv/models/research/slim</code>

Inserido o comando, pode-se salvar e fechar o arquivo. Para que as variáveis do sistema sejam atualizadas e detecte a API, digite o seguinte comando no terminal: 

<code>source ~/.bashrc</code>

Pode ser que o comando acima desative o seu ambiente virtual. Para testar a instalação da API ative novamente seu ambiente virtual e dentro da pasta <i>venv/models/research</i> digite:

<code>(venv) $ python object_detection/builders/model_builder_test.py</code>

Uma mensagem como a abaixo deve aparecer se tudo estiver instalado da maneira correta:

<i>Ran 21 tests in 0.178s <br>

OK</i>

<h2>4. Gerando os dados para o treinamento</h2>
<h3>4.1 Organizando as pastas</h3>
A partir de agora vamos considerar a pasta <i>venv/models/research/object_detection</i> como a nossa pasta raíz. Para facilitar o manuseio dos arquivos, sugiro criar algumas pastas dentro da mesma. São elas:

<b>images</b>: local para salvar as imagens de treino e teste;<br>
<b>inference_graph</b>: onde ficará o grafo de inferência, após a rede ter sido treinada;<br>
<b>training</b>: pasta onde o TensorFlow salvará todos os dados relacionados ao treinamento da rede.<br>

<h3> 4.2 Marcando as imagens e criando amostras para treinamento</h3>

Para a marcação das imagens, recomenda-se o <i>software</i> <a href="https://github.com/tzutalin/labelImg"> LabelImg</a>. Como saída para cada imagem, o programa gera um XML contendo as informações dos <i>bounding boxes</i> criados. Antes de gerarmos os TFRecords (arquivo utilizado como entrada da rede), é necessário converter estes XMLs para CSVs. Para isso, crie uma pasta chamada "train" e cole todas as imagens de treino, junto com os seus respectivos XMLs, gerados a partir do <i>LabelImg</i>. Para o teste, crie uma pasta chamada "test" e faça o mesmo procedimento. Mova ambas pastas para a "images", previamente criada. Feito isso, cole o arquivo "xml_to_csv.py" (encontrado neste repositório) na pasta raíz ("object_detection") execute <i>script</i> utilizando o seguinte comando:

<code> (venv) $ python xml_to_csv.py </code> <br>

Se tudo acontecer de maneira correta, as mensagens abaixo devem aparecer:
<br><br><i>Successfully converted xml to csv.<br>
Successfully converted xml to csv.</i>

Este processo criará os arquivos "train_labels.csv" e "test_labels.csv" na pasta "images". Agora, dentro da pasta "training" (também criada anteriormente) é necessário criar um arquivo com o nome "label_map.pbtxt". Dentro deste deve conter apenas o seguinte conteúdo:

```
item { 
  id: 1 
  name: 'class' 
} 
```

Substitua "class" pela classe desejada. Se houver mais de uma classe, deve-se adicionar abaixo deste um novo trecho alterando o "id" e o "name". Por exemplo:

```
item {
  id: 1
  name: 'dog'
}
 
item {
  id: 2
  name: 'cat'
}
```

E assim por diante. Agora é necessário criar os <i>TFRecords</i>, arquivos de entrada da rede. Para isso, utilize o <i>script</i> "generate_tfrecord.py" (também encontrado neste repositório). Cole-o na pasta "object_detection". Antes de executá-lo, é necessário alterar o seguinte trecho do código (linha 30):

```
# TO-DO replace this with label map 
def class_text_to_int(row_label): 
    if row_label == 'class': 
        return 1 
    else: 
        None 
```

Este trecho deve ser alterado de acordo com o que foi colocado no <i>label map</i>. Ou seja, se houverem mais classes, deve-se seguir o exemplo abaixo:

```
# TO-DO replace this with label map 
def class_text_to_int(row_label): 
    if row_label == 'dog': 
        return 1 
    elif row_label == 'cat': 
        return 2 
    else: 
        None
```

É <b>necessário</b> que as classes estejam na mesma ordem e com o mesmo <i>id</i> que foram colocados no arquivo <i>label_map.pbtxt</i>.

<h3> 4.3 Ajustando os arquivos necessários </h3>

O processo de treinamento será iniciado utilizando os pesos de modelos previamente treinados e fornecidos pela própria Google. Para isso, é necessário entrar no <a href="https://github.com/tensorflow/models/blob/master/research/object_detection/g3doc/detection_model_zoo.md">Model Zoo</a> e baixar o modelo <i>faster_rcnn_resnet101_coco</i> (ou baixá-lo diretamente por este <a href="http://download.tensorflow.org/models/object_detection/faster_rcnn_resnet101_coco_2018_01_28.tar.gz">link</a>). Este será o modelo utilizado como exemplo. Feito isso, descompacte o arquivo baixado e mova a pasta <i>faster_rcnn_resnet101_coco_2018_01_28</i> para a pasta raíz ("object_detection").

Agora, a partir da pasta raíz é necessário navegar até <i>samples/configs</i> e copiar o arquivo <i>faster_rcnn_resnet101_coco.config</i> (modelo de rede utilizada) para dentro da pasta "object_detection/training". Dentro do arquivo será necessário editar algumas linhas. Altere os valores como no exemplo abaixo:

<span>&#8226;</span> Linha 9 (Quantidade de classes) -> <i>num_classes: 1</i><br>
<span>&#8226;</span> Linha 12 (Dimensão mínima das imagens de entrada) -> <i>min_dimension: 512</i><br>
<span>&#8226;</span> Linha 13 (Dimensão máxima das imagens de entrada) -> <i>max_dimension: 512</i><br>
<span>&#8226;</span> Linha 106 (Local de onde serão carregados os pesos iniciais) -> <i>fine_tune_checkpoint: "faster_rcnn_resnet101_coco_2018_01_28/model.ckpt"</i><br>
<span>&#8226;</span> Linha 116 (Local onde estão os TFRecords relacionados ao treinamento) -> <i>input_path: "training/train.record"</i><br>
<span>&#8226;</span> Linha 118 (Local onde está o <i>label map</i> relacionado ao treinamento) -> <i>label_map_path: "training/label_map.pbtxt"</i><br>
<span>&#8226;</span> Linha 130 (Local onde estão os TFRecords relacionados ao teste) -> <i>input_path: "training/test.record"</i><br>
<span>&#8226;</span> Linha 132 (Local onde está o <i>label map</i> relacionado ao test) -> <i>label_map_path: "training/label_map.pbtxt"</i><br>
 
Feitas as alterações, basta salvar e fechar o arquivo.

<h3> 4.4 Treinando a rede </h3>

Com todos os arquivos devidamente ajustados, o próximo passo é treinar a rede neural. Para isso, execute o seguinte comando:

<code>(venv) $ python legacy/train.py --logtostderr --train_dir=training/ --pipeline_config_path=training/faster_rcnn_resnet101_coco.config</code>

Se tudo foi configurado da maneira correta, a rede deve iniciar seu treinamento. É importante salientar que pode demorar alguns segundos (ou minutos, dependendo da configuração do computador) para que a rede comece efetivamente a treinar. Além disso, a rede cria <i>checkpoints</i> de tempos em tempos, salvando-os dentro da pasta "training". Então o treinamento pode ser interrompido a qualquer momento pressionando "Crtl + C".

Caso você queira acompanhar o andamento do treinamento, pode-se utilizar o <i>Tensorboard</i> digitando o seguinte comando (em outro terminal):

<code>(venv) $ tensorboard --logdir=training</code>

A aplicação criará uma página web que pode ser acessada pelo endereço indicado pela ferramenta. Então basta abrir o navegador e digitar o endereço indicado pelo <i>Tensorboard</i>. Geralmente possui o seguinte formato: http://NomeDoPC:6006

<h3> 4.5 Exportando o grafo de inferência </h3>

Após finalizar o treinamento da rede neural, é necessário gerar o grafo de inferência. Este é utilizado para fazer as predições com o modelo devidamente treinado. Para exportá-lo, use o seguinte comando:

<code>python export_inference_graph.py --input_type image_tensor --pipeline_config_path training/faster_rcnn_resnet101_coco.config --trained_checkpoint_prefix training/model.ckpt-XXXX --output_directory inference_graph</code>

O trecho "XXXX" deve ser substituído pelo número de <i>steps</i> em que foi salvo determinado <i>checkpoint</i>. Ou seja, dentro da pasta "training" existirão alguns arquivos ".ckpt". Cada um deles é referente a um momento do treinamento da rede neural, e o número junto ao seu nome é o <i>step</i> em que ele foi salvo. O determinado trecho pode ser substituído por algum destes números, geralmente escolhendo-se o mais alto. Segue um exemplo:

<code>python export_inference_graph.py --input_type image_tensor --pipeline_config_path training/faster_rcnn_resnet101_coco.config --trained_checkpoint_prefix training/model.ckpt-50000 --output_directory inference_graph</code>

Caso a rede tenha treinado por 50000 <i>steps</i>.

<h3> 4.6 Fazendo predições com o modelo treinado </h3>

Para fazer predições utilizando o modelo treinado, pode-se utilizar o script "Object_detection_image.py" (encontrado neste repositório). Cole-o em na pasta raíz ("object_detection"). Antes de executá-lo, é necessário alterar algumas linhas. São elas:

<span>&#8226;</span> Linha 34 (Local/Nome da imagem) -> <i>IMAGE_NAME = 'images/test/test1.jpg'</i><br>
<span>&#8226;</span> Linha 50 (Quantidade de classes) -> <i>NUM_CLASSES = 1</i><br>

O <i>script</i> deve ser executado com o ambiente virtual ativado. Ou seja:

<code>(venv) $ python Object_detection_image.py</code>
