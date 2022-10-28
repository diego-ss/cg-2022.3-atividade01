<h1 align="center">Diego Sousa Santos - 11044616</h1>

<p align="center">
  <img  src="https://user-images.githubusercontent.com/55899445/194708500-d6d7288c-7e47-44f4-b2e0-af67640d99d9.png" width="150" height="150"/>
</p>

# ABCg

![linux workflow](https://github.com/hbatagelo/abcg/actions/workflows/linux.yml/badge.svg)
![macOS workflow](https://github.com/hbatagelo/abcg/actions/workflows/macos.yml/badge.svg)
![Windows workflow](https://github.com/hbatagelo/abcg/actions/workflows/windows.yml/badge.svg)
![WASM workflow](https://github.com/hbatagelo/abcg/actions/workflows/wasm.yml/badge.svg)
[![GitHub release (latest by date)](https://img.shields.io/github/v/release/hbatagelo/abcg)](https://github.com/hbatagelo/abcg/releases/latest)

Development framework accompanying the course [MCTA008-17 Computer Graphics](http://professor.ufabc.edu.br/~harlen.batagelo/cg/) at [UFABC](https://www.ufabc.edu.br/).

[Documentation](https://hbatagelo.github.io/abcg/abcg/doc/html/) \| [Release notes](CHANGELOG.md) 

ABCg is a lightweight C++ framework that simplifies the development of 3D graphics applications based on [OpenGL](https://www.opengl.org), [OpenGL ES](https://www.khronos.org), [WebGL](https://www.khronos.org/webgl/), and [Vulkan](https://www.vulkan.org). It is designed for the tutorials and assignments of the course "MCTA008-17 Computer Graphics" taught at Federal University of ABC (UFABC).

* * *

### Atividade 01 - SimpleCalculator
<details>
  <summary>EXPANDIR PARA VISUALIZAR</summary>

<p align="center">
    <img width="456" alt="simpleCalculatorIMG" src="https://user-images.githubusercontent.com/55899445/194708704-9e639cb6-2a07-48f9-84ef-48868aa4f470.PNG">
</p>

-   Link GitHub Pages: [SimpleCalculator](https://diego-ss.github.io/cg-2022.3-UFABC/simpleCalculator/)

### Descrição geral

-   O propósito da aplicação é simular o funcionamento de uma calculadora simples (com as operações básicas) com as técnicas apresentadas até então durante as aulas da disciplina, visando atender os requisitos propostos na atividade 1.
-   Para isso, existe uma tela com dois inputs que representam os números envolvidos na operação, com um combobox indicando as operações possíveis, dois botões para calcular e limpar os parâmetros e um label para exibir o resultado.
-   Os operadores e resultado são armazenados em variáveis da classe Window.

### Detalhes da implementação

#### Assets
-   Como assets auxiliares, foi utilizada a fonte Inconsolata-Medium como no projeto TicTacToe.
#### main.cpp
-   No arquivo main foi utilizada a implementação padrão que vimos em aula.
#### window.hpp
-   Para a definição da classe Window, foram sobrescritos dois métodos da classe OpenGLWindow (da qual Window tem herança): onCreate e onPaintUI.
```cpp
protected:
  void onCreate() override;
  void onPaintUI() override;
```
-   Além disso, foram definidas as seguintes variáveis e métodos auxiliares para o processamento:
```cpp
private:
  float operator1{0.0f}; // REFERE-SE AO VALOR NUMÉRICO DO PRIMEIRO OPERADOR
  float operator2{0.0f}; // REFERE-SE AO VALOR NUMÉRICO DO SEGUNDO OPERADOR
  float result{0.0f}; // ARMAZENA O RESULTADO DA OPERAÇÃO MATEMÁTICA
  std::string calcOperator{"+"}; // INDICADOR DE QUAL OPERAÇÃO SERÁ REALIZADA

  ImFont *m_font{}; // FONTE DE TEXTO

  void calculate(); // MÉTODO AUXILIAR PARA REALIZAR O CÁLCULO COM BASE NOS PARÂMETROS INFORMADOS
  void clear(); // MÉTODO AUXILIAR PARA LIMPAR OS DADOS DA CALCULADORA
```
#### window.cpp
-   Neste arquivo foram implementados os métodos sobrescritos e os novos definidos na classe Window, do arquivo window.hpp.
-   A ideia da composição da janela foi considerar uma tabela com seis linhas, todas com uma coluna apenas: 
    -  a primeira contém um input para que o usuário digite o primeiro número da operação.
    -  a segunda contém um combobox com as operações disponíveis. 
    -  a terceira contém um input para que o usuário digite o segunda número da operação.
    -  a quarta contém um botão que confirma a realização da operação.
    -  a quinta contém uma label que exibe o resultado da operação matemática.
    -  a sexta contém um botão responsável por limpar os operadores e o resultado.
-   No método onCreate é ralizado o import da fonte de texto e zerados os operadores.
```cpp
void Window::onCreate() {
  // Load font with bigger size for the X's and O's
  auto const filename{abcg::Application::getAssetsPath() +
                      "Inconsolata-Medium.ttf"};
  m_font = ImGui::GetIO().Fonts->AddFontFromFileTTF(filename.c_str(), 72.0f);
  if (m_font == nullptr) {
    throw abcg::RuntimeError{"Cannot load font file"};
  }

  clear();
}
```
-   No método clear, é realizada a limpeza dos operadores e do resultado.
```cpp
void Window::clear() {
  operator1 = 0.0f;
  operator2 = 0.0f;
  result = 0.0f;
}
```
-   No método calculate, é realizada a operação com base nos parâmetros informados pelo usuário.
```cpp
void Window::calculate() {
  // verificar o operador
  if (calcOperator == "+")
    result = operator1 + operator2;
  else if (calcOperator == "-")
    result = operator1 - operator2;
  else if (calcOperator == "*")
    result = operator1 * operator2;
  else if (calcOperator == "/")
    result = operator1 / operator2;
}
```
-   No método onPaintUI é onde está sendo realizada toda definição dos elementos de UI da tela da calculadora.
-   Para isso, dentro dele foram definidas as seguintes variáveis:
```cpp
      auto const buttonHeight{70}; // altura fixa dos botões
      static std::vector comboItems{"+", "-", "*", "/"}; // vetor de strings com as operações possíveis
      static std::size_t currentIndex{}; // índice da operação selecionada pelo usuário
```
-   Após isso é iniciada a tabela e definida cada uma de suas linhas. 
```cpp
        // primeira linha da tabela
        ImGui::TableNextRow();
        {
          // primeiro número da operação
          ImGui::TableSetColumnIndex(0);
          // ajustando largura do input
          ImGui::PushItemWidth(appWindowWidth);
          // input de float
          ImGui::InputFloat("op1", &operator1);
        }
```
```cpp
        // segunda linha da tabela
        ImGui::TableNextRow();
        {
          ImGui::TableSetColumnIndex(0);
          // combo do operador
          if (ImGui::BeginCombo("Combo Operador",
                                comboItems.at(currentIndex))) {
            for (auto index{0U}; index < comboItems.size(); ++index) {
              bool const isSelected{currentIndex == index};
              if (ImGui::Selectable(comboItems.at(index), isSelected)) {
                currentIndex = index;
                calcOperator = comboItems.at(currentIndex); // SETANDO A VARIÁVEL DO OPERADOR QUANDO ALGUM ITEM É SELECIONADO
              }

              if (isSelected)
                ImGui::SetItemDefaultFocus();
            }
            ImGui::EndCombo();
          }
        }
```
```cpp
        // terceira linha da tabela
        ImGui::TableNextRow();
        {
          // segundo número da operação
          ImGui::TableSetColumnIndex(0);
          // ajustando largura do input
          ImGui::PushItemWidth(appWindowWidth);
          // input de float
          ImGui::InputFloat("op2", &operator2);
        }
```
```cpp
        // quarta linha da tabela
        ImGui::TableNextRow();
        {
          // botão de realizar operação
          ImGui::TableSetColumnIndex(0);
          if (ImGui::Button("=", ImVec2(-1, buttonHeight))) {
            calculate(); // REALIZANDO O CÁLCULO QUANDO O BOTÃO É ACIONADO
          }
        }
```
```cpp
      // quinta linha da tabela
        ImGui::TableNextRow();
        {
          // resultado da operação
          ImGui::TableSetColumnIndex(0);
          ImGui::SetCursorPosX(
              (appWindowWidth -
               ImGui::CalcTextSize(std::to_string(result).c_str()).x) /
              2);
          ImGui::Text("%s", std::to_string(result).c_str()); // EXIBINDO O RESULTADO NA LABEL
        }
```
```cpp
        // sexta linha da tabela
        ImGui::TableNextRow();
        {
          // botão de limpar números
          ImGui::TableSetColumnIndex(0);
          if (ImGui::Button("clear", ImVec2(-1, buttonHeight))) {
            clear(); // LIMPANDO PARÂMETROS E RESULTADO
          }
        }
```
-   Para compilação web, foi utilizado o arquivo html do tictactoe adapatado para esta aplicação.

### Resultado Final
<p align="center">
    <img src="https://user-images.githubusercontent.com/55899445/194710962-51332d27-a383-4e27-aa58-99d1d52cf3b8.gif"/>
</p>


* * *
</details>

### Atividade 02 - Roll a Ball
<details>
  <summary>EXPANDIR PARA VISUALIZAR</summary>

<p align="center">
    <img width="456" alt="rollaballImage" src="https://user-images.githubusercontent.com/55899445/198695644-ec553618-34cb-4b7b-8207-4572cc3d21d8.png">
</p>

-   Link GitHub Pages: [RollABall](https://diego-ss.github.io/cg-2022.3-UFABC/rollaball/)

### Descrição geral

-   O propósito da aplicação é ser um jogo 2D cujo desenvolvimento e funcionalidades utilizem dos conceitos vistos em aula relacionados à gráficos 2D com primitivas do OpenGL, utilizando a biblioteca ABCg e renderização dos gráficos usando shaders.
-   As primitivas utilizadas resumem-se em triângulos, utilizados de forma singular para formar os espinhos e em conjunto para formar a bola.
-   O objetivo do jogo é conseguir o máximo de pontos, desviando dos espinhos ao decorrer do tempo. 
-   A bola se movimenta sozinha, e é possível utilizar a <b>seta para cima</b> para pular e a <b>barra de espaço</b> para acelerar.
-   Se a bola tocar em um dos espinhos, a partida termina e o jogo é reiniciado.

### Detalhes da implementação

#### Assets
-   Como assets auxiliares, foi utilizada a fonte Inconsolata-Medium como no projeto TicTacToe e no SimpleCalculator.
#### main.cpp
-   No arquivo main foi utilizada a implementação padrão que vimos em aula, removendo os  FPS, botão de tela cheia e utilizando 4 samples.
#### gamedata.hpp
-   Classe responsável por armazenar status e informações relevantes para as regras e funcionalidades do jogo.
-   Para sua definição, foram definidas as seguintes propriedades:
```cpp
  enum class Input { Up, Space }; // definição de enum para classificar os inputs possíveis
  enum class State { Playing, GameOver }; // definição de enum para classificar os status de jogo possíveis
  
  // struct para armazenar os dados do jogo
  struct GameData {
    State m_state{State::Playing}; // ESTADO DO JOGO
    std::bitset<2> m_input;        // ARRAAY DOS INPUTS
    int m_score;                   // PONTUAÇÃO
  };
```
#### window.hpp
-   Para a definição da classe Window, foram sobrescritos sete métodos da classe OpenGLWindow (da qual Window tem herança):
```cpp
  void onEvent(SDL_Event const &event) override; // PARA CAPTURAR EVENTOS DO TECLADO
  void onCreate() override; // AÇÕES AO CRIAR A APLICAÇÃO
  void onUpdate() override; // AÇÕES AO ATUALIZAR A JANELA
  void onPaint() override; // AÇÕES AO RENDERIZAR A JANELA
  void onPaintUI() override; // AÇÕES DE UI
  void onResize(glm::ivec2 const &size) override; // AÇÕES AO REDIMENSIONAR A JANELA
  void onDestroy() override; // AÇÕES AO DESTRUIR A JANELA
```
-   Além disso, foram definidas as seguintes variáveis e métodos auxiliares para o processamento:
```cpp
  glm::ivec2 m_viewportSize{}; // DIMENSÕES DA JANELA
  GLuint m_objectsProgram{}; // OBJECTS PROGRAM
  GameData m_gameData; // GAME DATA REF
  Ball m_ball; // BALL REF
  Spikes m_spikes; // SPIKES REF
  abcg::Timer m_restartWaitTimer; // TEMPORIZADOR
  ImFont *m_font{}; // FONTE
  std::default_random_engine m_randomEngine; // RANDOMIZADOR

  void restart(); // PARA REINICIAR AS CONDIÇÕES DE JOGO
  void checkCollisions(); // PARA VERIFICAR COLISÕES
```
#### window.cpp
-   Neste arquivo foram implementados os métodos sobrescritos e os novos definidos na classe Window, do arquivo window.hpp.
-   A ideia da composição da janela foi considerar o jogo e seus elementos renderizando e também dois elementos de UI: 
    -  um texto para contabilizar o Score.
    -  um texto que é exibido quando o jogo é finalizado. 
-   Os eventos capiturados são para as teclas SPACE, UP, W e para os botões do mouse.

- No método <b>OnEvent</b> são capturadas as interações do usuário com a interface:
```cpp
// Eventos do teclado
  if (event.type == SDL_KEYDOWN) {
    if (event.key.keysym.sym == SDLK_SPACE) // Capturando DOWN da barra de espaço
      m_gameData.m_input.set(gsl::narrow<size_t>(Input::Space)); // Setando o vetor de inputs do GameData
    if (event.key.keysym.sym == SDLK_UP || event.key.keysym.sym == SDLK_w) // capturando DOWN do w e seta 
      m_gameData.m_input.set(gsl::narrow<size_t>(Input::Up)); // Setando o vetor de inputs do GameData
  }
  if (event.type == SDL_KEYUP) {
    if (event.key.keysym.sym == SDLK_SPACE) // Capturando UP barra de espaço
      m_gameData.m_input.reset(gsl::narrow<size_t>(Input::Space)); // Setando o vetor de inputs do GameData
    if (event.key.keysym.sym == SDLK_UP || event.key.keysym.sym == SDLK_w) // capturando DOWN do w e seta 
      m_gameData.m_input.reset(gsl::narrow<size_t>(Input::Up)); // Setando o vetor de inputs do GameData
  }

  // Eventos do mouse
  if (event.type == SDL_MOUSEBUTTONDOWN) {
    if (event.button.button == SDL_BUTTON_LEFT)
      m_gameData.m_input.set(gsl::narrow<size_t>(Input::Space));
    if (event.button.button == SDL_BUTTON_RIGHT)
      m_gameData.m_input.set(gsl::narrow<size_t>(Input::Up));
  }
  if (event.type == SDL_MOUSEBUTTONUP) {
    if (event.button.button == SDL_BUTTON_LEFT)
      m_gameData.m_input.reset(gsl::narrow<size_t>(Input::Space));
    if (event.button.button == SDL_BUTTON_RIGHT)
      m_gameData.m_input.reset(gsl::narrow<size_t>(Input::Up));
  }
```
- No método <b>OnCreate</b> são realizadas as operações necessárias ao inicializar o ambiente, c:
```cpp

```
-   No método calculate, é realizada a operação com base nos parâmetros informados pelo usuário.
```cpp

```
-   No método onPaintUI é onde está sendo realizada toda definição dos elementos de UI da tela da calculadora.
-   Para isso, dentro dele foram definidas as seguintes variáveis:
```cpp

```
-   Após isso é iniciada a tabela e definida cada uma de suas linhas. 
```cpp

```
```cpp

```
```cpp

```
```cpp

```
```cpp

```
```cpp

```
-   Para compilação web, foi utilizado o arquivo html do tictactoe adapatado para esta aplicação.

### Resultado Final
<p align="center">
    <img alt="gif roll a ball" src="https://user-images.githubusercontent.com/55899445/198696680-c425a09f-5b33-4359-84a7-f06c1d6af755.gif"/>
</p>


* * *
</details>


## License

ABCg is licensed under the MIT License. See [LICENSE](https://github.com/hbatagelo/abcg/blob/main/LICENSE) for more information.
