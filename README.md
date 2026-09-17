# Projeto: Aplicação com persistência de dados em backend

(./projeto-demo.gif   "GIF animado mostrando a navegação no mapa, cadastro de ecoponto e traçado de rotas.")

## Acesso

- **Aplicação publicada:** https://elc1090.github.io/project2-2026b-FabricioDD/
- **Repositório GitHub:** https://github.com/elc1090/project2-2026b-FabricioDD

## Desenvolvedor(a)

- **Nome:** Fabricio Thomas Freitas Santos
- **Curso:** Sistemas de Informação - UFSM

---

## Proposta

Desenvolvimento de uma aplicação web Frontend e Backend, cujo objetivo é mapear e gerenciar pontos de coleta de resíduos recicláveis. A aplicação permite cadastrar, consultar, atualizar e excluir locais contendo informações como endereço, coordenadas geográficas, materiais aceitos e horário de funcionamento. 

O sistema conta com visualização em mapa, geolocalização do usuário e cálculo de rotas entre pontos de partida e destino.

## Parceria/cliente/usuário

- **Parceiro(a) / Cliente:** ARTHUR MORO FRÓES

## Feedback/comentário da parceria/cliente/usuário

**"Tá OK."**<br>
Arthur Moro,2026.

---

## Desenvolvimento

### Processo

Para este projeto, optei por Vanilla JS (JavaScript Puro) no Frontend e o Supabase (PostgreSQL) no Backend. Essa combinação eliminou muitas das complexidades de gerenciar um servidor dedicado, garantindo a persistência de dados em um banco relacional com API nativa e infraestrutura gratuita.

Durante o desenvolvimento, enfrentei e resolvi diversos desafios técnicos reais:

1. **Escolha do Provedor de Mapas:** Inicialmente, tentei utilizar o OpenStreetMap e o CartoDB como provedores de blocos (*tiles*). No entanto, devido a bloqueios de política de uso do servidor voluntário do OSM em ambiente de desenvolvimento local (`127.0.0.1` do Live Server) e exigência de chave de API do CartoDB. Solucionei o problema migrando para os servidores da **Esri (ArcGIS)**. Mais tarde, solucionei o problema com OpenStreetMap (troquei o navegador).

2. **Captura de Coordenadas e Usabilidade:** Para facilitar o cadastro pelo usuário sem exigir a digitação manual de coordenadas, implementei um evento de clique no mapa (`map.on('click')`) que captura a latitude e longitude exatas e preenche automaticamente os campos do formulário lateral.

3. **Gerenciamento de Zoom e Desempenho Visual:** Quando a visão do mapa está muito afastada (nível global/nacional), múltiplos pinos padrão podem poluir a tela. Desenvolvi uma lógica no evento `zoomend` que alterna dinamicamente os marcadores: em zoom distante (nível < 8), os locais são exibidos como pequenos círculos(`L.circleMarker`); em zoom próximo, revertem para pinos completos (`L.marker`) com balões informativos.

4. **Sistema de Rotas e Geolocalização:** Integrei a API nativa de Geolocalização do navegador para centralizar o mapa no usuário e utilizei o plugin *Leaflet Routing Machine* acoplado ao servidor OSRM para traçar trajetos automotivos. Modifiquei algumas pequenas coisas, como o icone do ponto de saída e botão para apagar a rota.

5. **CRUD Completo e Persistência:** Tratei o comportamento padrão de envio do formulário, garantindo requisições para inserção (`INSERT`), atualização (`UPDATE`) e remoção (`DELETE`) de registros via cliente do Supabase, com atualização em tempo real da camada do mapa.

---

## Trechos de código

### 1. Captura de coordenadas ao clicar no mapa e marcador temporário
<code>
// Preenche o formulário com a latitude/longitude do ponto clicado pelo usuário
map.on('click', function(e) {
    document.getElementById('latitude').value = e.latlng.lat;
    document.getElementById('longitude').value = e.latlng.lng;

    // Remove o pino de seleção anterior e adiciona o novo
    if (marcadorTemporario) { map.removeLayer(marcadorTemporario); }
    marcadorTemporario = L.marker(e.latlng).addTo(map)
        .bindPopup("Posição selecionada").openPopup();
});
</code>

###2. Alternância dinâmica de marcadores conforme o nível de zoom

<code>
// Alterna entre pinos completos e pequenos pontos para não poluir o mapa em zoom distante
function atualizarVisualizacaoZoom() {
    const zoomAtual = map.getZoom();
    if (zoomAtual < 8) {
        if (map.hasLayer(layerPinos)) map.removeLayer(layerPinos);
        if (!map.hasLayer(layerPontinhos)) map.addLayer(layerPontinhos);
    } else {
        if (!map.hasLayer(layerPinos)) map.addLayer(layerPinos);
        if (map.hasLayer(layerPontinhos)) map.removeLayer(layerPontinhos);
    }
}
map.on('zoomend', atualizarVisualizacaoZoom);
</code>

###3. Traçado de rotas com marcadores HTML totalmente personalizados

<code>
// Cria a rota viária utilizando a localização atual do usuário
controleRota = L.Routing.control({
    waypoints: [ L.latLng(latOrigem, lngOrigem), L.latLng(latDestino, lngDestino) ],
    language: 'pt',
    show: false,
    router: L.Routing.osrmv1({ serviceUrl: '[https://router.project-osrm.org/route/v1](https://router.project-osrm.org/route/v1)' }),
    createMarker: function(i, waypoint) {
        const isPartida = i === 0;
        const iconeInicio = L.divIcon({
            className: 'icone-partida',
            html: '🚗', 
            iconSize: [30, 30], 
            iconAnchor: [17, 17] // Centraliza no ponto do mapa
        });
        return L.marker(waypoint.latLng, { draggable: true, icon: icone });
    }
}).addTo(map);
</code>

---

## Tecnologias
### Linguagens e afins

    **HTML & CSS;**

    **JavaScript;**

    **Leaflet.js (v1.9.4):** Biblioteca open-source para renderização e interatividade de mapas;

    **Leaflet Routing Machine:** Plugin de roteamento viário baseado em OSRM (Open Source Routing Machine).

    **Supabase:** Cliente para conexão direta com o banco relacional PostgreSQL via API REST.

    **Provedores de Tiles:** Esri ArcGIS e Open Street Map.

### Ambiente de desenvolvimento

    **IDE:** Visual Studio Code (VS Code)

    **Servidor de Desenvolvimento Local:** Extensão Live Server

    **Gerenciador de Banco de Dados:** Painel Administrativo Supabase

    **Navegador:** Libre Wolf, Google Chrome

---

## Referências e créditos

    **Leaflet.js Documentation:** https://leafletjs.com/reference.html

    **Supabase JavaScript Client Docs:** https://supabase.com/docs/reference/javascript

    **Leaflet Routing Machine Tutorial:** https://www.gisatcontent.com/leaflet-routing-machine/

    **Esri ArcGIS Tiles Service:** https://www.esri.com/

    **OpenStreetMap:** Dados de mapa e imagens (tiles) cedidos por © [OpenStreetMap contributors](https://www.openstreetmap.org/copyright) sob a licença ODbL.