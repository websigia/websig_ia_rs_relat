Solução Implementada
==================================

======================
Organização dos Dados
======================
A estrutura do sistema está organizada em três camadas: 
camada de dados (GeoJSON), onde os dados geográficos e
alfanuméricos de casos de COVID-19 e de população estão
armazenados; camada aplicacional (GitHub), que permite
publicar na internet; e camada cliente (HTML, CSS e JavaScript,
incluindo Leaflet), que suporta as funcionalidades do
WebSIG e define como a informação é apresentada.


.. figure::  images/camadas.jpg
   :align:   center
         
   Figura 1 - Arquitetura do Projeto

=====================================================
Pré-processamento e preparação dos Dados Geográficos
=====================================================
Para carregar os dados dentro do projeto, a primeira etapa passou
por converter os dados para o formato GeoJSON. Para isso optou-se
por utilizar o software QGIS para efetuar um join dos dados com
a informação dos distritos em formato shapefile, sendo para isso
necessário adicionar em ambas as tabelas um campo em comum. No caso
optou-se pelo campo designado DD, que permite identificar unicamente
cada distrito. 

Foi necessário converter o sistema de coordenadas
dos dados originais para o sistema de coordenadas global WGS84, e de
seguida foi exportado para formato GeoJSON (Export - GeoJSON). O formato
GeoJSON é muito utilizado em serviços web, sendo facilmente interpretado
pela biblioteca de mapas que será utilizada no projeto - a Leaflet. Esta
biblioteca JavaScript open-source de fácil utilização disponibiliza
gratuitamente diversos recursos e plugins para a criação de mapas
interativos e permite adicionar vários tipos de camadas.

O ficheiro GeoJSON criado corresponde à camada de dados do projeto,
pois é o repositório dos dados apresentados no WebSIG.


===========================
Desenvolvimento do Projeto
===========================

Após o pré-processamento dos dados geográficos e de modo a visualizar no WebSIG
a informação geográfica presente no GeoJSON, o
passo seguinte incidiu na criação das páginas HTML, utilizando o editor
de texto Notepad++. Os ficheiros HTML têm uma estrutura base composta
pelas seguintes tags: html, head e body, delimitadas por ‘< ‘e ‘>’,
respeitando uma organização hierárquica, ou seja:

.. code-block:: html

    <html> 
        <head> 
        </head> 
        <body> 
        </body> 
    </html>

A tag <html> corresponde ao elemento de raiz de uma página HTML, <head> contém 
meta-informação e <body> define o corpo do documento, contendo todos os conteúdos
visíveis da página HTML (https://www.w3schools.com/html/html_intro.asp).

Para o desenvolvimento do projeto foram criados 4 ficheiros HTML correspondentes
aos quatro mapas com os casos de COVID-19 em Portugal continental entre setembro
e dezembro de 2020, e 1 ficheiro HTML que integra e estrutura a informação dos
restantes ficheiros na página do WebSIG. 

A opção de estruturar o projeto em 4 ficheiros HTML para os mapas e outro para a
sua integração permite que o código seja mais simples e a informação esteja mais 
organizada, facilitando a respetiva manutenção ou alteração. A estruturação do 
projeto em apenas um ficheiro HTML tornaria o código menos legível e dificultaria 
a gestão da informação. Na mesma perspetiva, os recursos do WebSIG estão organizados
em pastas, de forma a facilitar a sua consulta a manutenção.

Para a apresentação dos mapas no WebSIG optou-se por tirar partido de uma
biblioteca JavaScript que disponibilizasse as funcionalidades necessárias, 
tendo sido utilizada a Leaflet (https://leafletjs.com/). 

A estrutura, simbologia e funcionalidades dos mapas são definidas com recursos 
online da Leaflet, através da importação de um ficheiro CSS, que define os 
estilos do mapa, e de um ficheiro JavaScript, que define os aspetos 
interativos do mapa. Foram utilizados recursos disponibilizados online, tendo 
em conta que não houve necessidade de tornar a aplicação local. 

Os recursos online de CSS e JavaScript são acedidos através da inclusão no 
<head> das tags <link> e <script>, respetivamente. A tag “integrity” destina-se 
a assegurar que o recurso disponível naquele URL é o pretendido e não foi alterado.

.. code-block:: html

    <link rel="stylesheet" href="https://unpkg.com/leaflet@1.3.4/dist/leaflet.css"
        integrity="sha512-puBpdR0798OZvTTbP4A8Ix/l+A4dHDD0DGqYW6RQ+9jxkRFclaxxQb/SJAWZfWAkuyeQUytO7+7N4QKrDh+drA=="
        crossorigin=""/>

    <script src="https://unpkg.com/leaflet@1.3.4/dist/leaflet.js"
        integrity="sha512-nMMmRyTVoLYqjP9hrbed9S+FzjZHW5gY1TWCHA5ckwXZBadntCNs8kEqAWdrb9O7rxbCaA4lKTIWjDXZxflOcA=="
        crossorigin=""></script>

No <body>, é criada uma “div” para apresentação do mapa, ligando aos recursos
Leaflet através de JavaScript. 

.. code-block:: html

    <div id="map" style="height: 500px"></div>

    <script type="text/javascript">

            var map = L.map('map', {
              center: [39.694502, -8.130573],
              zoom: 7
          });

No bloco de código JavaScript onde é feita a integração com o Leaflet são
definidos vários aspetos referentes ao mapa, nomeadamente o ponto central e zoom
inicial do mapa (função L.map), origem dos tiles que formam o mapa de base e zoom máximo
e mínimo (função L.tileLayer), intervalos e cores das classes definidas
(função getColor), bem como o estilo (função style) e funcionalidades interativas
do mapa (função highlightFeature).

Para a ligação ao ficheiro GeoJSON, é definida no <head> a fonte dos dados.

.. code-block:: html

    <script type="text/javascript" src="dados/dados_covid_distr_simpl.geojson"></script>

A identificação da fonte e definições de como a informação constante do GeoJSON 
será apresentada no mapa é feita através da função L.geoJson.

.. code-block:: html

        geojson = L.geoJson(data, {
            style:style,
            onEachFeature: onEachFeature
        }).bindTooltip(function (layer){
            return layer.feature.properties.Distrito 
                   + '<p style="color:#2d862d">' + layer.feature.properties.Casos10k_2.toString() + ' casos por 10.000 hab. </p>';       
        }).addTo(map);

Os estilos da legenda do mapa estão definidos no ficheiro legendas.css, 
sendo a respetiva origem identificada no <head> através da tag <link>.

.. code-block:: html

    <link rel="stylesheet" href="css/legendas.css"/>

Foram definidas as características da legenda, nomeadamente a sua 
posição relativa (função L.control), intervalos de classes, título 
da legenda e labels (função onAdd). A legenda é adicionada ao mapa 
através da função legend.addTo(map).

.. code-block:: html

         var legend = L.control({position: 'bottomleft'});

          legend.onAdd = function (map) {
              var div = L.DomUtil.create('div', 'legend'),
              grades = [0, 10, 25, 50, 100, 150, 200]; 

              div.innerHTML = '<b>Casos por 10.000 hab. <br></b>';
              for (var i = 0; i < grades.length; i++) {
                  div.innerHTML +=
                  '<i style="background:' + getColor(grades[i] + 1) + '"></i>' +
                  grades[i] + (grades[i + 1] ? '&ndash;' + grades[i + 1] + '<br>' : '+');
              }
              return div;
          };

          legend.addTo(map);

O ficheiro HTML que agrega os 4 mapas designa-se “index.html”, de modo 
a ser reconhecido como o ficheiro HTML principal pelo servidor web. 
Os estilos da página web estão definidos no <head> através da tag 
<style>, onde estão indicadas as características do corpo da página 
(tag body), o alinhamento dos elementos (tag div) e estilo do rodapé 
(tag footer). É também criada a classe .mapa, que define as dimensões e 
características que cada mapa terá.  

.. code-block:: html

    <style>
        html,         
        body {
            padding: 0;
            margin: 0;
            background-color: #e6e6e6;
            font-family: Arial, Helvetica, sans-serif;
            }

        div {
            text-align: center;
            }  

        .mapa {
            width: 50%;
            height: 550px;
            float: left;
            margin-bottom: 20px;
            } 

        footer {
            text-align: center;
            margin-top: 200px;
            }  
    </style>

No <body> são criadas as div para o cabeçalho e os 4 mapas, com as respetivas 
características.
O cabeçalho corresponde a uma tabela (tag table), com a inserção de uma 
imagem correspondente ao logotipo do IGOT (tag img).

.. code-block:: html

    <div>
        <table style="width:100%; border-spacing: 20px;" >
            <tr>
                <td>
                    <img src="img/logo-igot-cor.png" style="width: 296px;height: 85px;" />
                </td>
                <td>
                    <h4>Mestrado em Sistemas de Informação Geográfica e Modelação Territorial aplicados ao
                    Ordenamento</p>WEBSIG 2020/2021</h4>
                    <h2>Distribuição de casos de COVID-19 em Portugal continental (Set. a Dez. 2020)</h2>
                    <div style="text-align: left;">
                    Dados de casos de COVID-19 por 10.000 habitantes em Portugal Continental, por distrito, entre setembro e dezembro de 2020. Trabalho realizado no âmbito da Unidade Curricular de WEBSIG ........
                    </div>
                </td>
            </tr>
        </table>
    </div>

Para a integração da informação dos 4 HMTL referentes aos mapas, 
foi utilizada a tag iframe, tendo sido criada uma div para cada um, 
com a definição do título, fonte, entre outras características.

.. code-block:: html

    <div>
        <div class="mapa">
            <h3>SETEMBRO 2020</h3>
            <iframe frameBorder="0" src="index_set.html" width="95%" height="95%" title="SETEMBRO"></iframe>
        </div>
        div class="mapa">
            <h3>OUTUBRO 2020</h3>
            <iframe frameBorder="0" src="index_out.html" width="95%" height="95%" title="OUTUBRO"></iframe>
        </div>
        <div class="mapa">
            <h3>NOVEMBRO 2020</h3>
            <iframe frameBorder="0" src="index_nov.html" width="95%" height="95%" title="NOVEMBRO"></iframe>
        </div>
        <div class="mapa">
            <h3>DEZEMBRO 2020</h3>
            <iframe frameBorder="0" src="index_dez.html" width="95%" height="95%" title="DEZEMBRO"></iframe>
        </div>
    </div>

O rodapé foi criado através da tag footer.

.. code-block:: html

    <footer>
        <p><b>Autoras:</b> Inês Almiro & Raquel Santos</p>
    </footer>


======================
Publicação do Projeto
======================
Para apresentar cartograficamente os dados relativos ao
número de infetados associados à COVID-19, por distrito, na internet,
foi utilizado o GitHub, uma plataforma para alojamento de código, muito
utilizada por programadores. O GitHub permite criar gratuitamente um
repositório, bem como publicar o projeto online.
Corresponde à camada aplicacional do projeto desenvolvido, funcionando como servidor de HTTP.

No âmbito do projeto foi criado um repositório designado “websig_ia_rs”,
onde foram inseridos os ficheiros respeitantes ao projeto (https://github.com/websigia/websig_ia_rs.git).
O projeto está disponível no endereço URL fornecido pelo 
GitHub: https://websigia.github.io/websig_ia_rs/.



