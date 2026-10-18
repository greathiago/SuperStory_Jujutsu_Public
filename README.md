# SuperStory - Jujutsu | Compilado Completo do Sistema e Arquitetura

> **Documento de Referência Técnica para Desenvolvedores e Agentes de IA**  
> **Última Atualização:** Setembro de 2026  
> **Plataforma:** Firecast SDK 3.7b (Lua 5.3 / XML / Delphi FMX / NDB)

---

## 1. Visão Geral do Projeto & Guia do Ecossistema para IAs

O **SuperStory - Jujutsu** é um plugin de ficha de personagem de alto nível para o aplicativo Firecast RPG, derivado da arquitetura da engine modular **SuperStory** e ambientado no universo de feitiçaria e maldições de *Jujutsu Kaisen*.

* **Pasta Raiz do Plugin:** `Plugins/SuperStory - Jujutsu`
* **Formulário Principal:** `Jujutsu Kaisen/SuperStory_Jujutsu.lfm`
* **ID do Módulo (`module.xml`):** `SuperStoryJujutsu`
* **Nome do Módulo:** `SuperStory - Jujutsu`
* **DataType do Formulário Principal:** `SuperStory_Jujutsu`
* **Tema Visual Padrão:** `theme="dark"` (Paleta escura moderna: `#09090B`, `#141417`, `#18181B`, roxo `#A855F7`, ciano `#38BDF8`, verde `#22C55E`, amarelo `#EAB308`).

### 1.1. Mindset e Lógica Correta para Agentes de IA (Como Raciocinar Neste Sistema)
Se você é uma Inteligência Artificial encarregada de balancear, programar ou alterar qualquer coisa neste sistema:
1. **Postura Científica e Analítica:** Não adote clichês ou abstrações de sistemas de fantasia tradicionais (D&D, Tormenta, etc.). Pense e aja como um **físico pesquisador e programador**: toda mecânica, efeito ou habilidade é uma manifestação estrita da mecânica clássica ($E_k = \frac{1}{2}mv^2$, $p=mv$), ciência dos materiais (tensão de escoamento $\sigma_y$, trabalho de fratura $U_T$), termodinâmica, eletrofisiologia neural e cinemática. Entretanto, isso não deve afetar sua escolha de palavras para escrever informações do sistema, jovens não versados na física que vão le-las, portanto os textos devem ser legiveis para eles. Na dúvida, o estilo de escrita deve ser igual ao do mestre da mesa Hero Sekai (eu).

2. **Escala da Grid da Mesa:** Cada quadrado da grid da mesa mede **$0.5\text{ metros}$ ($50\text{ cm}$)** (largura biacromial média humana). **NUNCA** assuma $1.5\text{ m}$. Todos os deslocamentos, alcances, empurrões e raios são calibrados em múltiplos exatos de $0.5\text{ m}$.
3. **Proporção Rígida de Eficiência Temporal:** Ação Padrão ($100\% = 1.0$) $\to$ Ação Bônus ($\times 66.6\% \approx 0.667$) $\to$ Efeito Passivo ($\times 50\%$ da Bônus $= 33.3\% \approx 0.333$). Proporção universal rígida **$3 : 2 : 1$**.
4. **Natureza da Redução de Dano (RD):** Não existe "RD plástica". A Dureza (RD Geral) é universal contra qualquer tipo de dano recebido. Reduções específicas (RD Macia para contusão, RD Térmica para fogo) concedem o dobro de rendimento mecânico ($+4$ por ponto em vez de $+2$).

### 1.2. Mapeamento do Ecossistema: O Que Olhar e Onde Olhar
Para não se perder ou vasculhar pastas desnecessariamente, consulte os arquivos conforme o objetivo da tarefa:

| Finalidade / O que Buscar | Onde Olhar (Caminho) | Papel e Conteúdo |
| :--- | :--- | :--- |
| **Anatomia, Atributos e Perícias** | `Guia_Atributos_e_Pericias_Jujutsu.docx` | Documento conceitual canônico do sistema: tabela de atributos, perícias, especialidades, IMC, regras de reforço e conceitos anatômicos. |
| **Cálculo de Dano, Deslocamento e Especialidades** | `Macros/Outros/modJujutsu.lua` | **Macro da Verdade Matemática:** Contém os cálculos de dados de soco/chute ($faces = \max(4, 4 + \lfloor Lançamento/2.5 \rfloor \times 2)$), curva exponencial de deslocamento ($2^{(Arr+Cad)/15}$), ações bônus de cadência ($\lfloor Cadencia/4 \rfloor$), carga máxima e peso. |
| **Gerenciamento de Barras e Defesas** | `Macros/Outros/barCalcJujutsu.lua` | Macro chamada pela ficha para atualizar barras de PV, E.A., Estamina, Juízo, Vontade e defesas (CE/CA). |
| **Lista Canônica de Efeitos Balanceada** | `Plugins/Lista_de_Efeitos_Jujutsu.rtf` <br> `Plugins/Lista_de_Efeitos_Jujutsu_FichaBasica.xml` | Tabela definitiva de efeitos ofensivos, defensivos, utilitários, ontológicos e passivos com decomposição atômica fracionária exata. |
| **Código-Fonte do Plugin da Ficha** | `Plugins/SuperStory - Jujutsu` | Estrutura de telas XML/LFM e centralização de dados em `tabela_atributos.lua`. |
| **Sala Oficial no Firecast** | Sala **Jujutsu Kaisen - Unblessed** (`roomId: 263949`) | Pasta `[02] - O Sistema` (ex: ficha/NPC `10 - Lista Efeitos`). Conexão de IA via bot local (`firecast-ai-bot`). |

Apesar da terminalogia de "definitivo", muitas coisas escritas aqui e nos arquivos .docx e .rtf podem estar desatualizadas. Confie em códigos e em textos recentemente sincronizados das fichas.

### 1.3. Inspirações vs Divergências Críticas (Hero Sekai vs Jujutsu)
* **O que a Hero Sekai É (`Plugins/HeroSekai` e Sala `262809`):** Inspiração estética e ergonômica da interface (layout de abas, botões laterais `+`/`-` das barras, separação de inventário 50/50). O NPC `02 - Criando Habilidades` na Hero Sekai serve apenas para entender o *tom* descritivo e estrutural de criação de técnicas.
* **O que a Hero Sekai NUNCA É:** A Hero Sekai **NÃO** dita os valores matemáticos de Jujutsu.
  * O dano da Hero Sekai (ex: $1d6+10$) é incompatível e desbalanceado para o PV de Jujutsu.
  * Na Hero Sekai a mecânica de PV, RD plástica e pesos de atributos são completamente diferentes.
  * Em Jujutsu, o PV base é $41.2$ e escala cubicamente com a tenacidade dos materiais; a Potência escala as faces de dados e cadência; e o Output de E.A. custa $2\times$ XP. **Sempre prevalecem as regras e a física de Jujutsu presentes neste compilado e em `modJujutsu.lua`.**

---

## 2. Estrutura de Arquivos e Telas

O formulário raiz `SuperStory_Jujutsu.lfm` carrega um `tabControl` alinhado ao client com as 8 abas temáticas:

| Aba | Arquivo Fonte | Descrição e Recursos |
| :--- | :--- | :--- |
| **1. Feiticeiro** | `1_Personagem.xml` | Informações biográficas (Nome, Grau, Clã/Origem, Sexo, Idade, Altura, Afiliação), Nick Firecast, Avatar editável com preview proporcional, campo de Música Tema (OST URL) com botão de reprodução externa (`GUI.openInBrowser`), e botões de Exportação/Importação direta da ficha em XML via `NDB.exportXML` / `NDB.importXML`. |
| **2. Atributos e Perícias** | `2_Atributos.xml` | **O coração do sistema.** Matriz 5x3 com 15 atributos, hexágonos visuais de modificadores, lista de perícias fixas por atributo com popups de rolagem e cálculo de XP, recordList dinâmico de perícias de Conhecimento, barras de recursos (PV, EA, Extra, Estamina, Juízo, Vontade), RD única e painel de Atributos Naturais (Peso Atleta, Gordura Corporal, Slider de Beleza, BodyType, defesas CE/CA e Especialidades). |
| **3. Jujutsu** | `3_Jujutsu.xml` | Detalhes da Técnica Inata (Nome, Tipo, Rank), contadores de PH (Pontos de Habilidade gastos/usados) e `recordList` vertical de Habilidades/Feitiços (`habilidade.lfm`) com suporte a imagem, rank, tipo e custo. |
| **3.5. Técnicas** | `3.5_Tecnicas.xml` | Apenas um `recordList` vertical de Habilidades/Técnicas (`habilidade.lfm`) com suporte a imagem, rank, tipo e custo. Clone da página de Jujutsu |
| **4. Vantagens e Desvantagens** | `4_Vantagens.xml` | Painéis duplos de Vantagens e Desvantagens com contadores de pontos, `recordList` (`vantagensRow.lfm`) e card de inspeção lateral com detalhes dinâmicos. |
| **5. Inventário** | `5_Inventario.xml` | Sistema de inventário dividido em duas seções verticais perfeitamente proporcionais (50/50 via `onResize`): "Itens Equipados" e "Itens na Mochila". Itens com peso, quantidade, efeito/dano e popup de edição (`itemRow.lfm`). |
| **6. Background** | `6_Background.xml` | História do personagem, anotações de aliados/rivais e sistema de Contatos em formato de cartões visuais (`contatoRow.lfm`) com foto grande proporcional, botão flutuante de exclusão "X" no canto superior direito e popup de vínculo/relação. |
| **7. Anotações** | `7_Anotacoes.xml` | Bloco de texto livre (`textEditor`) com fundo escuro para anotações de campanha. |
| **8. Cálculos / Outros** | `8_Calculos.xml` | Painel financeiro/karma, histórico de XP com `recordList` (`xpHistoryRow.lfm`), botão e popup centralizado de adicionar XP com motivo e consolidadores automáticos de XP Gasto em Atributos (`XP_GASTO_ATTR`) e Perícias (`Gasto_P`). |

---

## 3. Mecânicas Físicas e Ciência dos Materiais (SuperStory Jujutsu)

Diferente de sistemas convencionais de fantasia, o SuperStory Jujutsu é estritamente alicerçado na mecânica clássica, dinamometria e ciência dos materiais.

### 3.1. A Matriz de Atributos (5 Linhas x 3 Colunas)

| Linha / Função | Coluna 1: Muscular / Físico | Coluna 2: Mental | Coluna 3: Cursed Energy (E.A.) |
| :--- | :--- | :--- | :--- |
| **PODER** | **Massa Muscular** | Conhecimento | **Output de E.A.** *(Custo XP 2x)* |
| **CONTROLE** | **Coordenação** | Astúcia | Controle de E.A. |
| **VELOCIDADE** | **Potência** | Cognição | Ativação de E.A. |
| **RESISTÊNCIA** | **Resistência** | Juízo | Densidade de E.A. |
| **ENERGIA** | **Vigor** | Vontade | Eficiência de E.A. |

### 3.2. Perícias Físicas, Mentais e Energéticas (Nomenclatura Rigorosa)

```
[ COLUNA 1: MUSCULAR / FÍSICO ]
• Massa Muscular:      Porte (Superiores)      | Carga (Inferiores/Coluna) | Fauce (Mandíbula/Mordida/Agarre)
• Coordenação:         Delicadesa (Fina)       | Molejo (Base Corporal)    | Acrobacia (Sem Superfície)
• Potência:            Lançamento (Membros Sup)| Arranque (Membros Inf)    | Cadência (Frequência)
• Resistência:         Dureza (Escoamento/RD)  | Resiliência (Deformação/PV)| Saúde (Homeostase/Toxinas)
• Vigor:               Fôlego (Oxigênio)       | Estamina (Reserva Energética)

[ COLUNA 2: MENTAL ]
• Conhecimento:        RecordList Dinâmico alimentado por TABELA_CONHECIMENTO centralizada
                       (Jujutsu, Psicologia, Medicina, Criminalística, Engenharia, Mecânica,
                        Computação, Química, Biologia, Zoologia, Botânica, Culinária)
• Astúcia:             Lábia (Social Ativo)    | Percepção (Sensorial)     | Dissimulação (Ocultação)
• Cognição:            Reflexos (Resposta)     | Raciocínio (Processamento)| Taquiloquia (Velocidade de Fala/Canto)
• Juízo:               Estoicismo (Dor/Choque) | Sensatez (Manipulação)    | Coragem (Terrores)
• Vontade:             Inspiração (Liderança)  | Persistência (Reserva)    | Proatividade (Iniciativa)

[ COLUNA 3: CURSED ENERGY (E.A.) ]
• Output de E.A. (2x): Volume (Dano/Tamanho)   | Impulso (Velocidade/Distância)
• Controle de E.A.:    Direcionamento (Precisão)| Modelagem (Novos Formatos) | Supressão (Ocultação de Fluxo)
• Ativação de E.A.:    Canalização (Preparação)| Cooldown (Recarga/Combos)
• Densidade de E.A.:   Solidez (RD Adicional)  | Tenacidade (Multiplicador de Defesas)
• Eficiência de E.A.:  Aproveitamento (Custo)  | Fluxo (Circulação Energética)
```

- **Regra de Custo do Output:**
  - Devido à versatilidade extrema de potencializar todo o Reforço de E.A., o **Output** possui `xpMultiplier = 2`:
    - **1 ponto N (Base):** Custa **20 XP** (em vez de 10).
    - **1 ponto Extra na Perícia (`volume` / `impulso`):** Custa **100 XP** (em vez de 50).
- **Interface e Recursos:**
  - **Barras Principais (PV, E.A., EXTRA, ESTAMINA):** Botões ergonômicos laterais `+` e `-` (estilo HeroSekai) empilhados à direita de cada progressBar.
  - **Barras Secundárias (Juízo e Vontade):** Alinhamento perfeito em qualquer resolução: Juízo cobre rigorosamente a largura de PV + E.A., e Vontade cobre EXTRA + ESTAMINA. Botões laterais `+` e `-` integrados ao `setBars`.
  - **Redução de Dano (RD):** Duplicação no painel direito com "RD:" (`rd_natural + rd_bonus = rd_total`) e "RD macia:" (`rdMacia_natural + rdMacia_bonus = rdMacia_total`, calculada a partir de massa e resiliência).
- **Centralização de Conhecimento:**
  - Definida canonicamente em `tabela_atributos.lua` (`TABELA_CONHECIMENTO` e `ORDEM_CONHECIMENTO`), consumida dinamicamente por `2_Atributos.xml` e `periciaRow.lfm`, eliminando duplicações hardcoded.

### 3.3. Arquitetura de Dados dos Atributos: A Separação dos Pontos S
Para evitar que recálculos biomecânicos automáticos sobrescrevam valores digitados pelos jogadores, a ficha e as macros operam em uma equação de quatro termos:

$$\mathbf{Total} = \mathbf{N} + \mathbf{E} + \mathbf{S} + \mathbf{X} \quad \implies \quad \mathbf{Mod} = \left\lfloor \frac{\mathbf{Total}}{10} \right\rfloor$$

* **Pontos N (Naturais / Investidos):** `[attr]_n`. O investimento permanente de XP do personagem ($1\text{ pt } N = 10\text{ XP}$, exceto Output onde $1\text{ pt } N = 20\text{ XP}$).
* **Pontos E (Extras Manuais do Jogador):** `[attr]_e`. Campo **100% preservado** para digitação manual livre do jogador (equipamentos, pactos, buffs de feitiços e dádivas narrativas). O código do sistema **nunca** sobrescreve os campos `_e`.
* **Pontos S (Sistema / Biomecânica Automática):** `[attr]_s` (além de `ce_s` e `ca_s`). Valores computados deterministicamente pelas leis da física e biomecânica do sistema (altura, alometria cúbica, latência neural, inércia da potência e projeção de silhueta).
  - **Interface e Proteção:** Nos popups de atributos da ficha (`tabela_atributos.xml`), na barra de CE e na barra de CA, os campos `_s` possuem `readOnly="true"`.
  - **Cópia Instantânea:** Ao clicar em um campo de Pontos S, o evento `onClick` executa `System.setClipboardText(tostring(sheet[key] or 0))` copiando seu valor numérico diretamente para a área de transferência do usuário, mantendo a consistência com os hexágonos centrais.
* **Pontos X (Temporários / Efeitos Rápidos):** `[attr]_x`. Buffer de estados transitórios e transformações de batalha acionados via macros (ex: `transformJujutsu.lua`).

---

## 4. Formulação Matemática Universal

### 4.1. Sistema de Pontos de Vida (PV) e Tenacidade dos Materiais
O PV total representa o trabalho de deformação mecânica até a ruptura estrutural:
$$\mathbf{PV} = \sqrt[3]{\text{Massa (kg)} \times U_T\ (\text{J/kg})}$$
$$\text{Taxa de Equivalência: } 1\text{ PV} \equiv \sqrt[3]{1\text{ Joule}} \iff E_{\text{Joules}} = \mathbf{PV}^3$$

Em função dos valores totais contínuos da ficha (com raiz nula exata em $T = -100$ / $Mod = -10$):
$$\text{Massa (kg)} = 70 \times \left(\frac{\text{Massa}_t + 100}{100}\right)^3$$
$$U_T\ (\text{J/kg}) = 1.000 \times \left(\frac{\text{Resiliência}_t + 100}{100}\right)^3$$
$$\mathbf{PV} = \mathbf{41.2} \times \left(\frac{\text{Massa}_t + 100}{100}\right) \times \left(\frac{\text{Resiliência}_t + 100}{100}\right) + \text{PV}_{\text{bônus}}$$

#### Tabela de PV por Material e Modificador

| Modificador ($M / R$) | Material Representativo | Massa ($m$) | Resiliência ($U_T$) | Energia Total ($E_{\text{frac}}$) | PV Resultante | Comportamento Físico |
| :---: | :--- | :---: | :---: | :---: | :---: | :--- |
| **$-10$** | **Água / Fluido Ideal** | Qualquer | $0\text{ J/kg}$ | $0\text{ J}$ | **$0\text{ PV}$** | Sem integridade à tração |
| **$-8$** | **Inseto / Quitina Pequena** | $0.005\text{ kg}$ | $3.000\text{ J/kg}$ | $15\text{ J}$ | **$2.5\text{ PV}$** | Esmagado com soco leve |
| **$-3$** | **Criança / Pessoa Frágil** | $24\text{ kg}$ | $343\text{ J/kg}$ | $8.232\text{ J}$ | **$20.2\text{ PV}$** | Baixa massa e resiliência |
| **$0$** | **Carne Humana Adulta (70kg)**| $\mathbf{70\text{ kg}}$ | $\mathbf{1.000\text{ J/kg}}$ | $\mathbf{70.000\text{ J}}$ | **$\mathbf{41.2\text{ PV}}$** | **Padrão Humano Saudável** |
| **$+2$** | **Atleta / Marcialista** | $120\text{ kg}$ | $1.728\text{ J/kg}$ | $207.360\text{ J}$ | **$59.3\text{ PV}$** | Músculos densos |
| **$+4$** | **Carvalho / Osso Reforçado** | $192\text{ kg}$ | $2.744\text{ J/kg}$ | $526.848\text{ J}$ | **$80.8\text{ PV}$** | Estrutura de madeira/osso |
| **$+10$** | **Concreto Armado / Grau 1** | $560\text{ kg}$ | $8.000\text{ J/kg}$ | $4.48\times 10^6\text{ J}$| **$164.8\text{ PV}$** | Nanami, Todo, Mei Mei |
| **$+18$** | **Aço Estrutural / Toji** | $1.536\text{ kg}$| $21.952\text{ J/kg}$| $3.37\times 10^7\text{ J}$| **$323.0\text{ PV}$** | Grau Especial Físico |
| **$+30$** | **Titânio / Grau Especial** | $4.480\text{ kg}$| $64.000\text{ J/kg}$| $2.86\times 10^8\text{ J}$| **$659.2\text{ PV}$** | Couraça pesada de maldição |
| **$+48$** | **Matéria Amaldiçoada Divina**| $13.680\text{ kg}$| $195.112\text{ J/kg}$| $2.67\times 10^9\text{ J}$| **$1.387+\text{ PV}$**| **Sukuna / Gojo no Ápice** |

---

### 4.2. Sistema de Dano Físico e Energia Cinética ($E = \frac{1}{2}mv^2$)
O Dano Base físico é a raiz cúbica da energia cinética entregue:
$$\text{Dano Base} = \sqrt[3]{E_{\text{cinética}}} = \sqrt[3]{\frac{1}{2} \cdot m_{\text{efetiva}} \cdot v^2}$$
- Humano padrão ($Mod = 0$): $m_{\text{efetiva}} = 7\text{ kg}$, $v = 6\text{ m/s} \implies E_k = 126\text{ J} \implies \sqrt[3]{126} = \mathbf{5.01\text{ Dano}}$ (média exata de **$2\text{d}4$**).

#### A Interação na Rolagem de Ataque:
- **Membros Superiores (Socos):** Velocidade via **Lançamento** e Massa via **Porte**.
  $$\text{Faces } (X) = \max\left(4, \;\; 4 + \left\lfloor \frac{\text{Lançamento}_m}{2.5} \right\rfloor \times 2\right)$$
  $$N = 2 + \left\lfloor \frac{\text{Porte}_m}{4} \right\rfloor, \quad Y = \text{Porte}_m \times 2 + \left\lfloor \frac{\text{Massa}_m}{2} \right\rfloor$$
- **Membros Inferiores (Chutes):** Velocidade via **Arranque** e Massa via **Carga**.
  $$\text{Faces } (X) = \max\left(4, \;\; 4 + \left\lfloor \frac{\text{Arranque}_m}{2.5} \right\rfloor \times 2\right)$$
  $$N = 2 + \left\lfloor \frac{\text{Carga}_m}{4} \right\rfloor, \quad Y = \text{Carga}_m \times 2 + \left\lfloor \frac{\text{Massa}_m}{2} \right\rfloor + 2$$

#### Tabela de Attack Potency e Rolagens

| Modificador ($M / P$) | Energia Cinética ($E_k$) | Fórmula de Rolagem | Dano Médio | Categoria no Universo Jujutsu |
| :---: | :---: | :---: | :---: | :--- |
| **$0 / 0$** | $\mathbf{\approx 126\text{ J}}$ | **$2\text{d}4$** | **$5.0$** | **Humano Comum Desarmado** |
| **$+2 / +2$** | $\approx 1.000\text{ J}$ | **$2\text{d}4 + 5$** | **$10.0$** | Atleta / Pugilista profissional |
| **$+4 / +4$** | $\approx 5.000\text{ J}$ | **$3\text{d}6 + 10$** | **$20.5$** | Marcialista de elite / Yuji pré-CE |
| **$+8 / +8$** | $\approx 80.000\text{ J}$ | **$4\text{d}10 + 20$** | **$42.0$** | Parede / Quebrar pilares de concreto |
| **$+12 / +12$** | $\approx 10^6\text{ J}$ (MJ) | **$5\text{d}12 + 30$** | **$62.5$** | Edifício / Grau 1 (Nanami, Todo) |
| **$+18 / +18$** | $\approx 10^8\text{ J}$ (100 MJ) | **$6\text{d}20 + 45$** | **$108.0$** | Quarteirão / Toji, Maki, Hakari |
| **$+25 / +25$** | $\approx 10^{10}\text{ J}$ (10 GJ) | **$8\text{d}20 + 65$** | **$149.0$** | Grau Especial Intermediário / Yuta |
| **$+35 / +35$** | $\approx 10^{12}\text{ J}$ (TJ) | **$10\text{d}20 + 90$** | **$195.0$** | Cidade / Fuga de Sukuna, Hollow Purple |
| **$+48 / +48$** | $\mathbf{\ge 10^{14}\text{ J}}$ (100 TJ) | **$14\text{d}20 + 125$** | **$272.0$** | **Ápice Divino (Sukuna 20D / Gojo Satoru)** |

---

### 4.3. Relação Biomecânica Massa $\to$ Potência Natural: A Lei do Quadrado-Cubo Cúbica
Na fisiologia muscular clássica (A. V. Hill, 1938; Schmidt-Nielsen, 1984), a força contrátil isométrica máxima ($F_{\max}$) escala com a área de seção transversal fisiológica do músculo ($\text{PCSA} \propto L^2$), enquanto o volume e a massa corporal escalam cubicamente ($M \propto L^3$). A aceleração angular de um membro depende da relação torque/inércia rotacional:
$$a = \frac{\tau}{I} = \frac{F \cdot r}{m \cdot r^2} \propto \frac{L^2 \cdot L}{L^3 \cdot L^2} \propto L^{-2}$$

Para substituir a antiga parábola simplificada e modelar de forma fidedigna a escala contínua de massa muscular sem fragmentação, foi implementada a fórmula polinomial cúbica contínua no domínio pré-divisão ($t$):

$$P_{natural\_t}(M_t) = \frac{18000 \cdot M_t - 255 \cdot M_t^2 - M_t^3}{13750}$$

O resultado é arredondado e injetado em `sheet.potencia_s` (preservando `sheet.potencia_e` 100% livre para o jogador):
$$\text{potencia\_s} = \left\lfloor P_{natural\_t}(M_t) + 0.5 \right\rfloor$$

#### Comportamento da Curva por Faixa de Massa:
* **$M_t = 0$ ($Mod = 0$):** $P_t = 0 \to \text{potencia\_s} = 0$ (Baseline de um indivíduo sedentário/normal).
* **$M_t = 15$ ($Mod = +1.5$):** $P_t = 15.22 \to \text{potencia\_s} = +15$ ($+1\text{ Mod}$ de Potência por atletismo inicial).
* **$M_t = 25$ ($Mod = +2.5$):** $P_t = 20.00 \to \text{potencia\_s} = +20$ ($+2\text{ Mod}$ de Potência, físico de velocista/pugilista).
* **$M_t = 30$ ($Mod = +3.0$):** $P_t = 20.62 \to \text{potencia\_s} = +21$ (**Vértice/Pico Máximo de Potência Biomecânica**; ponto de transição onde a inércia da massa iguala o ganho de força contrátil).
* **$M_t = 40$ ($Mod = +4.0$):** $P_t = 18.04 \to \text{potencia\_s} = +18$ ($+1\text{ Mod}$, declínio de aceleração por hipertrofia pesada).
* **$M_t = 50$ ($Mod = +5.0$):** $P_t = 10.00 \to \text{potencia\_s} = +10$ ($+1\text{ Mod}$, limite fisiológico de fisiculturista pesado).
* **$M_t > 50$ ($Mod > +5.0$):** A inércia domina completamente a aceleração, penalizando a potência natural caso não haja intervenção de técnicas ou Restrição Celestial.

---

### 4.4. Cálculo Realista do Peso Corporal
O peso em quilogramas integra a Massa Efetiva contínua (média entre **Porte** e **Carga**):
$$M_{\text{efetivo}} = \frac{\text{Porte}_{\text{cont}} + \text{Carga}_{\text{cont}}}{2}$$
$$\text{Peso Final (kg)} = \text{Peso Ideal} \times \left(1 + 0.08 \times M_{\text{efetivo}}\right) \times \left(1 + \frac{\text{Gordura} - \text{GorduraPadrão}}{100}\right)$$

Onde o $\text{Peso Ideal} = h^2 \times \text{IMC}_{\text{base}}$ ($21.0$ para mulheres, $22.5$ para homens), evitando qualquer duplicação do peso base da gordura.

---

### 4.5. Biomecânica da Altura e Alometria de Atributos Naturais
A estatura de referência do ser humano no sistema é $H_0 = 1.75\text{ m}$. Qualquer variação de altura $\Delta H = H - 1.75$ altera a fisiologia do organismo de maneira realista nos Pontos S (`_s`):

1. **Massa Muscular (`massa_s`) — Alometria Isométrica Cúbica ($H^3$):**
   O volume anatômico de um corpo geometricamente similar escala com o cubo da altura linear ($V \propto H^3$):
   $$volRatio = \left(\frac{H}{1.75}\right)^3 - 1.0$$
   $$\text{massa\_s} = \left\lfloor 25 \times volRatio + 0.5 \right\rfloor$$
   *(Aos $1.90\text{ m}$: $\left(\frac{1.90}{1.75}\right)^3 - 1 \approx +0.280 \implies 25 \times 0.280 = \mathbf{+7\text{ pts } t} = +0.7\text{ Mod}$ de Massa Muscular).*

2. **Cognição (`cognicao_s`) — Condução Axonal e Latência Neuroanatômica:**
   A velocidade de condução de potenciais de ação em axônios mielinizados ($50\text{ a }100\text{ m/s}$, Hodgkin-Huxley) introduz latência proporcional ao comprimento da via neural entre os proprioceptores periféricos, medula e o córtex somatossensorial:
   $$\text{cognicao\_s} = \left\lfloor -\frac{100 \cdot (H - 1.75)}{3} + 0.5 \right\rfloor$$
   *(Aos $1.90\text{ m}$: $\Delta H = +0.15\text{ m} \implies -\frac{15}{3} = \mathbf{-5\text{ pts } t} = \mathbf{-0.5\text{ Mod}}$ de Cognição).*

3. **Vigor (`vigor_s`) — Fisiologia Cardiorrespiratória e Carga Hidrostática:**
   Conforme a Lei de Kleiber (1932) e o escalonamento alométrico respiratório ($VO_2\text{ max} \propto M^{-1/4}$), organismos maiores possuem menor taxa metabólica específica de oxigênio por unidade de massa. Além disso, a gravidade impõe maior coluna hidrostática ($\Delta P = \rho g H$) para retorno venoso cardíaco e perfusão encefálica:
   $$\text{vigor\_s} = \left\lfloor -\frac{100 \cdot (H - 1.75)}{3} + 0.5 \right\rfloor$$
   *(Aos $1.90\text{ m}$: $\Delta H = +0.15\text{ m} \implies \mathbf{-5\text{ pts } t} = \mathbf{-0.5\text{ Mod}}$ de Vigor).*

---

### 4.6. Projeção de Silhueta e Classes Defensivas (CE e CA Mágica)

1. **Projeção de Silhueta e Debuff Visual de Esquiva (`ce_s`):**
   A área da projeção ortogonal frontal do corpo escala quadraticamente com a altura ($A_{\text{frontal}} \propto H^2$), aumentando o ângulo sólido no campo de visão do atacante e reduzindo a incerteza de mira balística:
   $$\Delta A\% = \left(\frac{H}{1.75}\right)^2 - 1.0$$
   $$\text{ce\_s} = -2.80 \times \Delta A\%$$
   *(Aos $1.90\text{ m}$: $\left(\frac{1.90}{1.75}\right)^2 - 1 = +17.88\% \implies \text{ce\_s} = -2.80 \times 0.1788 = \mathbf{-0.50}$).*

2. **Classe de Esquiva (CE): Defesa Física e Reação:**
   A Cognição concede $+0.5$ do Modificador Base e $+0.5$ através da perícia de Reflexos, totalizando **$+1.0$ de CE por ponto de modificador cognitivo**:
   $$\text{perReflexosBonus} = \text{ex\_reflexos} + \text{ga\_reflexos} + \text{x\_reflexos}$$
   $$\text{reflexosCont} = \text{modCogCont} + \text{perReflexosBonus}$$
   $$\text{ce\_base} = 10 + \frac{\text{modPotCont}}{2} + \frac{\text{modCogCont}}{2} + \frac{\text{reflexosCont}}{2} = 10 + \frac{\text{modPotCont}}{2} + \text{modCogCont} + \frac{\text{perReflexosBonus}}{2}$$
   $$\mathbf{CE}_{\mathbf{total}} = \left\lfloor \text{ce\_base} + \text{ce\_s} + \text{ce\_extra} \right\rfloor$$
   *(Harmonia Pré-Arredondamento: aos $1.90\text{ m}$, a Cognição gera $-0.50$ e a Silhueta gera $-0.50$, totalizando $-1.00$ de debuff em CE antes do `math.floor`)*.

3. **Classe de Ativação Mágica (CA Mágica): Barreiras e Feitiçaria:**
   Mede a rapidez cognitiva e fluidez canalizadora para ativar domínios e feitiços antes da interrupção inimiga:
   $$\text{ca\_base} = 10 + \frac{\text{modAtivCont}}{2} + \frac{\text{modCogCont}}{2} + \frac{\text{reflexosCont}}{2} = 10 + \frac{\text{modAtivCont}}{2} + \text{modCogCont} + \frac{\text{perReflexosBonus}}{2}$$
   $$\mathbf{CA}_{\mathbf{total}} = \left\lfloor \text{ca\_base} + \text{ca\_s} + \text{ca\_extra} \right\rfloor$$

---

### 4.7. Cinemática de Deslocamento e Speed Blitz
Turno de corrida: $2.7\text{ segundos}$. Fator de conversão: $[\text{km/h}] = [\text{m/turno}] \times \frac{4}{3}$. Disparada dobra ($2\times$) o deslocamento base:

$$\text{Disparada (m/turno)} = 18 \times \left(\frac{\text{Arranque}_m + 10}{10}\right)^{0.6} \times \left(\frac{\text{Cadência}_m + 10}{10}\right)^{0.6} \times 2^{\left(\frac{\text{Arranque}_m + \text{Cadência}_m}{15}\right)}$$
$$\text{Deslocamento Base (m/turno)} = \frac{\text{Disparada}}{2}$$

- Em $-10$: **$0.0\text{ m/turno}$** (imobilidade total).
- Em $0$: Base **$9.0\text{ m/turno}$** (Disparada $= 18.0\text{ m/turno} = 24.0\text{ km/h}$).
- Em $+4$ (Usain Bolt): Disparada de **$33.4\text{ m/turno} = 44.5\text{ km/h} \le 44.7\text{ km/h}$**.
- Em $+8$: **Speed Blitz** ($\ge 3\times$ velocidade de humano normal, rompe a reação de $200\text{ ms}$).
- Em $+44\text{ a }+48$: **Hipersônico+ (Mach 12 a 21 - Gojo e Sukuna)**.

---

### 4.8. Dureza e Tensão de Escoamento ($\sigma_y$ em MPa)
A Dureza mede a tensão de escoamento elástico superficial ($\sigma_y$ em $\text{MPa} = \text{N/mm}^2$):

| Material / Superfície | Tensão de Escoamento ($\sigma_y$) | Força Crítica ($F_c$) | RD Dureza no RPG |
| :--- | :---: | :---: | :---: |
| **Carne e Pele Humana** | $1 \text{ a } 3\text{ MPa}$ | $\approx 2\text{ a }6\text{ kN}$ | **$0$** |
| **Madeira de Carvalho** | $40\text{ MPa}$ | $\approx 80\text{ kN}$ | **$4$** |
| **Osso Cortical Humano** | $120\text{ MPa}$ | $\approx 240\text{ kN}$ | **$8$** |
| **Aço Estrutural (A36)** | $250\text{ MPa}$ | $\approx 500\text{ kN}$ | **$16$** |
| **Titânio Grau 5** | $900\text{ MPa}$ | $\approx 1.800\text{ kN}$ | **$25$** |
| **Aço Balístico (AR500)**| $1.400\text{ MPa}$ | $\approx 2.800\text{ kN}$ | **$35$** |
| **Matéria Amaldiçoada Especial**| $\mathbf{100.000\text{ MPa}}$| $\mathbf{200.000\text{ kN}}$| **$\mathbf{60+}$** |

---

## 5. Arquitetura Modular de Macros

1. **`barCalcJujutsu.lua` (`Macros/Outros/barCalcJujutsu.lua`):**
   - Chamado automaticamente na ficha para gerenciamento de barras essenciais: PV, EA, Estamina, RD única, CA, CE e dependências. Integra a alometria de altura, Potência cúbica via `potencia_s`, debuff de silhueta `ce_s`, e classes defensivas reativas.
2. **`/mod` (`modJujutsu.lua` em `Macros/Outros/modJujutsu.lua`):**
   - Macro independente desacoplada que calcula o sumário detalhado de especialidades: Dano Desarmado (Socos e Chutes), Deslocamento, Disparada, Ações Bônus de Cadência, Carga Máxima e Peso Atualizado, formatando no chat e em `sheet.especialidades`.
3. **`transformJujutsu.lua` (`Macros/Outros/transformJujutsu.lua`):**
   - Macro de buff/transformação rápida que computa a soma unificada $T = N + E + S + X$ em alvos selecionados na mesa.
4. **`sync_tabela.py` (`sync_tabela.py`):**
   - Script gerador da matriz de atributos XML que injeta os popups de atributos com a coluna Pontos S, suporte a clipboard e layout expandido de $650\text{px}$.
5. **Popup BodyType (`bodyTypePopup`):**
   - Incorpora a tabela de materiais com presets de Resiliência e Dureza para customização de corpos inumanos e construtos.

---

## 6. Sincronização e Compilação

* **Script de Sincronização Automática (`sync_tabela.ps1`):**
  - Atualiza `tabela_atributos.xml` a partir de `tabela_atributos.lua`.
* **Compilação e Instalação Local:**
  - `rdk -c` (compilar)
  - `rdk -i` (compilar e instalar no Firecast)

---

## 7. Referências Científicas e Bibliográficas

1. **Hill, A. V. (1938).** *The heat of shortening and the dynamic constants of muscle.* Proceedings of the Royal Society of London. Series B - Biological Sciences, 126(843), 136-195. (Fundamentação da relação hiperbólica força-velocidade muscular).
2. **Galilei, Galileo (1638).** *Discorsi e Dimostrazioni Matematiche Intorno a Due Nuove Scienze.* (Primeira formulação histórica da Lei do Quadrado-Cubo e seus limites em estruturas biológicas).
3. **Kleiber, Max (1932).** *Body size and metabolism.* Hilgardia, 6(11), 315-353. (Escalonamento alométrico metabólico da taxa de oxigênio $\propto M^{3/4}$).
4. **Schmidt-Nielsen, Knut (1984).** *Scaling: Why is Animal Size so Important?* Cambridge University Press. (Tratado clássico de alometria, gasto energético em locomoção e hemodinâmica comparada).
5. **Hodgkin, A. L., & Huxley, A. F. (1952).** *A quantitative description of membrane current and its application to conduction and excitation in nerve.* The Journal of Physiology, 117(4), 500-544. (Cinética iônica de potenciais de ação e latência por comprimento axonal).
6. **Guyton, A. C., & Hall, J. E. (2020).** *Tratado de Fisiologia Médica.* 14ª Edição, Elsevier. (Capítulos de regulação hemodinâmica da pressão arterial, gravidade sobre colunas hidrostáticas e adaptações cardíacas ao volume corporal).

