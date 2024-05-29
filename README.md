![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/7aae8f82-23f9-4467-bb16-eef6f1daaea1)
Jungle Jump- um jogo de plataforma na tradição de clássicos como Super Mario Bros. 
Usando o nó CharacterBody2D
Usando o nó Camera2D
Combinando animações e entrada do usuário para produzir um comportamento de personagem complexo
Projetando níveis usando TileMap
Criando um plano de fundo de rolagem infinita usando ParallaxLayer
Transição entre cenas
Organizando seu projeto e planejando a expansão
Configurando o projeto
Para criar um novo projeto, comece abrindo Configurações do Projeto para que você possa configurar os padrões necessários.

Os ativos de arte para este jogo usam um estilo de pixel art, o que significa que eles ficam melhores quando as imagens não são suavizadas, que é a configuração padrão de Godot para filtragem de textura:
![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/c8a3157c-a5f0-4309-85df-c8bb9b6f5d7e)
Embora seja possível definir isso em cada , é mais conveniente especificar a configuração padrão. Clique no botão de alternância Avançado no canto superior direito e localize a seção Renderização/Texturas à esquerda. Na lista de configurações, role até a parte inferior e localize a configuração Texturas da tela/Filtro de textura padrão. Mude-o de Linear para Mais Próximo.Sprite2D

Em seguida, em Exibição/Janela, altere Esticar/Modo para itens de tela e Aspecto para expandir. Essas configurações permitirão que o usuário redimensione a janela do jogo, preservando a qualidade da imagem. Quando o projeto estiver concluído, você poderá ver os efeitos dessa configuração.

Em seguida, você pode configurar as camadas de colisão. Como este jogo terá vários tipos diferentes de objetos de colisão que precisam interagir de maneiras diferentes, você usará o sistema de camadas de colisão de Godot para ajudar a organizá-los. As camadas serão mais convenientes de usar se lhes forem atribuídos nomes, então vá para a seção Layer Names | 2D Physics e nomeie as quatro primeiras camadas assim (digitando diretamente na caixa ao lado do número da camada):

![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/13ef2099-a936-43c1-8398-a6059de71740)
 Definindo nomes de camadas físicas
 ![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/eaa1097d-f35f-4bf0-9db5-75a57adf2fbd)
 Certifique-se de usar os nomes exatos para as ações de entrada, pois você fará referência a elas no código mais tarde.

Isso é tudo o que você precisa definir em Configurações do projeto. Mas antes de começar a fazer a cena do jogador, você precisa aprender sobre um tipo diferente de nó de física.

Introdução aos corpos cinemáticos
Um jogo de plataforma requer gravidade, colisões, saltos e outros comportamentos físicos, então você pode pensar que essa seria a escolha perfeita para implementar o movimento do personagem. Na prática, você descobrirá que a física mais realista do corpo rígido não é desejável para um personagem de plataforma. Para o jogador, o realismo é menos importante do que o controle responsivo e uma sensação de ação. Então, como desenvolvedor, você quer ter controle preciso sobre os movimentos do personagem e a resposta de colisão. Por esta razão, um estilo cinemático de física é geralmente a melhor escolha para um personagem de plataforma.RigidBody2D

O nó é projetado para implementar corpos físicos que devem ser controlados diretamente via código. Esses nós detectam colisões com outros corpos quando eles se movem, mas não são afetados por propriedades físicas globais, como gravidade ou atrito. Isso não significa que eles não possam ser afetados pela gravidade e outras forças – apenas que você deve calcular essas forças e seus efeitos em código; O mecanismo de física não moverá um nó automaticamente.CharacterBody2DCharacterBody2D

Ao mover um nó como com , você não deve definir sua propriedade diretamente. Em vez disso, você deve usar os ou métodos fornecidos pelo corpo. Esses métodos movem o corpo ao longo de um determinado vetor e o param instantaneamente se uma colisão for detectada com outro corpo. Cabe então a você decidir sobre qualquer resposta de colisão.CharacterBody2DRigidBody2Dpositionmove_and_collide()move_and_slide()

Resposta à colisão
Após uma colisão, você pode querer que o corpo quique, deslize ao longo de uma parede ou altere as propriedades do objeto que atingiu. A maneira como você lida com a resposta de colisão depende de qual método você usa para mover o corpo:

move_and_collide()
Ao usar esse método, a função retorna um objeto após a colisão. Este objeto contém informações sobre a colisão e o corpo em colisão. Você pode usar essas informações para determinar a resposta. Observe que a função retorna quando o movimento é concluído com êxito sem colisão.KinematicCollision2Dnull

Por exemplo, se você quiser que o corpo salte do objeto em colisão, use o seguinte script:

extends CharacterBody2D
velocity = Vector2(250, 250)
func _physics_process(delta):
    var collision = move_and_collide(velocity * delta)
    if collision:
        velocity = velocity.bounce(collision.get_normal())

Explain
move_and_slide()
O deslizamento é uma opção muito comum para resposta de colisão. Imagine um jogador se movendo ao longo de uma parede em um jogo de cima para baixo, ou correndo ao longo do chão em um jogo de plataformas. Embora seja possível codificar a resposta você mesmo depois de usar o , fornece uma maneira conveniente de implementar o movimento deslizante. Ao usar esse método, o corpo deslizará automaticamente ao longo da superfície de um objeto em colisão. Além disso, colisões deslizantes permitirão detectar a orientação da superfície usando métodos como is_on_floor().move_and_collide()move_and_slide()

Uma vez que este projeto exigirá que você permita que o personagem do jogador corra ao longo do chão e suba / desça encostas, vai desempenhar um grande papel no movimento do seu jogador.move_and_slide()

Agora que você já entendeu o que são corpos cinemáticos, você vai usar um para fazer o personagem para este jogo.

Criando a cena do jogador
O nó Godot que implementa movimento cinemático e colisão é chamado de CharacterBody 2D.

Abra uma nova cena e adicione um nó nomeado como raiz e salve a cena. Não se esqueça de clicar no botão Agrupar Nó(s) Selecionado(s). Ao salvar a cena, você também deve criar uma nova pasta para contê-la. Isso ajudará a manter sua pasta de projeto organizada à medida que você adiciona mais cenas e scripts.CharacterBody2DPlayerPlayer

Veja as propriedades de no Inspetor. Observe os valores padrão de Modo de movimento e Direção para cima. O modo "aterrado" significa que o corpo considerará uma direção de colisão como o "chão", a parede oposta como o "teto" e quaisquer outras como "paredes" – que é determinada pela direção acima.CharacterBody2D

Como você fez em projetos anteriores, você incluirá todos os nós que o personagem do jogador precisa para funcionar na cena do Player. Para este jogo, isso significa lidar com colisões com vários objetos do jogo, incluindo plataformas, inimigos e colecionáveis; exibir animações para ações, como correr ou pular; e anexar uma câmera para seguir o jogador ao redor do nível.

O script das várias animações pode rapidamente se tornar incontrolável, então você precisará usar uma máquina de estado finito (FSM) para gerenciar e rastrear o estado do jogador. 

Camadas de colisão e máscaras
A propriedade Collision/Layer de um corpo define em que camadas(ões) no mundo da física o corpo é encontrado. precisa ser atribuído à camada "player" (que você nomeou em Configurações do projeto). Da mesma forma, Colisão/Máscara define quais camadas o corpo pode "ver" ou interagir. Se um objeto estiver em uma camada que não esteja na Máscara do jogador, o jogador não interagirá com ele.Player

Defina a Camada do jogador para o jogador e a Máscara para o ambiente, inimigos e itens. Clique nos três pontos à direita para abrir uma lista de caixas de seleção mostrando os nomes atribuídos às camadas:
![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/6c76ab46-ba51-43ff-934a-31cc9d8d1d38)
 Configuração de camadas de colisão

Isso garantirá que o jogador esteja na camada "player" para que outros objetos possam ser configurados para detectar o jogador ou não. Definir o valor da Máscara para todas as três camadas significa que o jogador poderá interagir com objetos em qualquer uma dessas camadas.

Sobre o AnimationPlayer
No início deste livro, você costumava exibir as animações baseadas em quadros de seus personagens. Esta é uma ótima ferramenta, mas só é útil para animar a textura visual de um nó. E se você quiser também animar qualquer uma das outras propriedades de um nó?AnimatedSprite2D

É aí que entra. Este nó é uma ferramenta muito poderosa para criar animações que podem afetar vários nós ao mesmo tempo; Você pode modificar qualquer uma de suas propriedades.AnimationPlayer

Animações
Para configurar as animações do personagem, siga estas etapas:

Adicione um nó ao . Arraste o arquivo da estação de ancoragem FileSystem e solte-o na propriedade Texture. A animação do jogador será salva na forma de uma folha de sprite:Sprite2DPlayerres://assets/player_sheet.png
![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/6bb58914-8d26-43e5-b325-72b20a84f320)
Folha de sprite

Você usará para manipular as animações, portanto, nas propriedades Animation do , defina HFrames como . Em seguida, defina Frame para ver o jogador em pé. Finalmente, mova-se para cima até que seus pés estejam de pé no chão, definindo Posição como . Isso tornará mais fácil codificar as interações do jogador mais tarde, porque você saberá que a propriedade do jogador representa a localização de seus pés.AnimationPlayerSprite2D197Sprite2D(0, -16)position
Adicione um nó à cena. Você usará esse nó para alterar a propriedade Frame de para os valores apropriados para cada animação.AnimationPlayerSprite2D
Antes de começar, examine as diferentes partes do painel Animação:
![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/e8865fa6-4116-47e7-84c3-a1bdefcbd26f)
O painel Animação

Clique no botão Animação e selecione Novo. Nomeie a nova animação como ociosa.
Defina seu Comprimento como segundos. Clique no ícone Loop para tornar o loop de animação e defina o Modo de Atualização da faixa como Contínuo.0.4
Altere a propriedade Frame de para , que é o primeiro quadro da animação ociosa, e clique no ícone de quadro-chave ao lado da propriedade para adicionar uma faixa de animação com um novo quadro-chave:Sprite2D7
![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/694a1543-33c2-4d23-b7d3-418f496259c6)
 Adicionando um quadro-chave

Deslize o depurador de reprodução para (você pode ajustar o controle deslizante de zoom no canto inferior direito para facilitar a localização). Adicione um quadro-chave para o quadro, que é o último quadro de ocioso.0.310
Pressione o botão Reproduzir para ver a animação ser reproduzida. Se não parecer correto, volte para o parágrafo anterior e certifique-se de ter seguido as etapas exatamente, especialmente o fato de que você começa no quadro e termina no quadro 10.7
Agora, repita esse processo para as outras animações. Consulte a tabela a seguir para obter uma lista de suas configurações:
![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/5f2e1570-c478-4e6f-a95d-2d1e4d50af3c)
Há também animações na folha de sprite para agachar e escalar, mas você pode adicioná-las mais tarde, uma vez que o movimento básico é concluído.

Forma de colisão
Como acontece com outros corpos, precisa de uma forma atribuída para definir seus limites de colisão. Adicione um nó e crie um novo dentro dele. Ao dimensionar a forma, você quer que ela atinja a parte inferior da imagem (os pés do jogador), mas seja um pouco mais estreita do que a imagem do jogador. Em geral, tornar a forma um pouco menor do que a imagem resultará em uma sensação melhor ao jogar, evitando a experiência de acertar algo que parece que não resultaria em uma colisão.CharacterBody2DCollisionShape2DRectangleShape2D

Você também precisará compensar a forma uma pequena quantidade para torná-la adequada. Definir a posição do nó para funciona bem. Quando terminar, deve ser aproximadamente assim:CollisionShape2D(0, -10)

![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/6b75b7da-c89f-4719-8321-5e4033c5b0e0)
Forma de colisão do jogador

Várias formas

Em alguns casos, dependendo da complexidade do seu personagem e de suas interações com outros objetos, convém adicionar várias formas ao mesmo objeto. Você pode ter uma forma nos pés do jogador para detectar colisões no solo, outra em seu corpo para detectar danos e ainda outra cobrindo a frente do jogador para detectar contato com paredes.

Finalizando a cena do jogador
Adicione um nó à cena. Este nó manterá a janela do jogo centrada no jogador à medida que ele se move ao redor do nível. Você também pode usá-lo para ampliar o jogador, já que a pixel art é relativamente pequena em comparação com o tamanho da janela do jogo. Lembre-se, como você define a opção de filtragem em Configurações do projeto, a textura do player permanecerá pixelada e bloqueada quando ampliada.Camera2DPlayer

Para habilitar a câmera, defina a propriedade Enabled como On e defina Zoom como . Valores menores que 1 reduzem o zoom da câmera, enquanto valores maiores ampliam.(2.5, 2.5)

Você verá um retângulo roxo-rosado ao redor do jogador. Esse é o retângulo da tela da câmera e mostra o que a câmera verá. Você pode ajustar a propriedade Zoom para aumentar ou diminuir seu tamanho para ver mais ou menos do mundo ao redor do jogador.

Estados do jogador
O personagem do jogador tem uma grande variedade de comportamentos, como pular, correr e agachar. Codificar tais comportamentos pode se tornar muito complexo e difícil de gerenciar. Uma solução é usar variáveis booleanas ( ou , por exemplo), mas isso leva a estados possivelmente confusos (e se e são ambos?) e rapidamente leva ao código _spaghetti_.is_jumpingis_runningis_crouchingis_jumpingtrue
Aqui está um diagrama dos estados do jogador e as transições entre eles:
![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/1716a40f-14db-42c2-8e68-8a6ce4d50bcf)
Diagrama de estado do jogador

Como você pode ver, diagramas de estado podem se tornar bastante complexos, mesmo com um número relativamente pequeno de estados.

Outros estados

Observe que, embora a folha de sprite contenha animações para eles, os estados e não estão incluídos aqui. Isso é para manter o número de estados gerenciáveis no início do projeto. Mais tarde, você terá a oportunidade de adicioná-los.CROUCHCLIMB

Script do jogador
Anexe um novo script ao nó. Observe que a caixa de diálogo mostra uma propriedade Template com o Movimento Básico padrão de Godot para esse tipo de nó. Desmarque a caixa Modelo – você não precisará desse código de exemplo para este projeto.Player

Adicione o código a seguir para iniciar a configuração da máquina de estado do jogador
Quando você quiser alterar o estado do jogador, você pode chamar change_state():enum
extends CharacterBody2D
@export var gravity = 750
@export var run_speed = 150
@export var jump_speed = -300
enum {IDLE, RUN, JUMP, HURT, DEAD}
var state = IDLE
func _ready():
    change_state(IDLE)
func change_state(new_state):
    state = new_state
    match state:
        IDLE:
            $AnimationPlayer.play("idle")
        RUN:
            $AnimationPlayer.play("run")
        HURT:
            $AnimationPlayer.play("hurt")
        JUMP:
            $AnimationPlayer.play("jump_up")
        DEAD:
            hide()
            Por enquanto, o script altera apenas qual animação está sendo reproduzida, mas você adicionará mais funcionalidade de estado mais tarde.

Movimento do jogador
O jogador precisa de três controles: esquerda, direita e salto. A comparação do estado atual com o qual as teclas são pressionadas acionará uma alteração de estado se a transição for permitida pelas regras do diagrama de estado. Adicione a função para processar as entradas e determinar o resultado. Cada condição representa uma das transições no diagrama de estado:get_input()if
func get_input():
    var right = Input.is_action_pressed("right")
    var left = Input.is_action_pressed("left")
    var jump = Input.is_action_just_pressed("jump")
    # movement occurs in all states
    velocity.x = 0
    if right:
        velocity.x += run_speed
        $Sprite2D.flip_h = false
    if left:
        velocity.x -= run_speed
        $Sprite2D.flip_h = true
    # only allow jumping when on the ground
    if jump and is_on_floor():
        change_state(JUMP)
        velocity.y = jump_speed
    # IDLE transitions to RUN when moving
    if state == IDLE and velocity.x != 0:
        change_state(RUN)
    # RUN transitions to IDLE when standing still
    if state == RUN and velocity.x == 0:
        change_state(IDLE)
    # transition to JUMP when in the air
    if state in [IDLE, RUN] and !is_on_floor():
        change_state(JUMP)
        Observe que a verificação de salto está usando em vez de . Enquanto o segundo retorna enquanto a tecla é pressionada, o primeiro só está no quadro depois que a tecla foi pressionada. Isso significa que o jogador deve pressionar a tecla de salto cada vez que quiser pular.is_action_just_pressed()is_action_pressed()truetrue

Chame esta função de , adicione a força da gravidade ao do jogador e chame o método para se mover:_physics_process()velocitymove_and_slide()
func _physics_process(delta):
    velocity.y += gravity * delta
    get_input()
    move_and_slide()
Lembre-se, uma vez que a propriedade Up Direction está definida como , qualquer colisão abaixo dos pés do jogador será considerada o "chão", e será definida como . Você pode usar esse fato para detectar quando o salto termina adicionando isso logo após move_and_slide():(0, -1)is_on_floor()truemove_and_slide()
if state == JUMP and is_on_floor():
    change_state(IDLE)
    O salto ficará melhor se a animação mudar de para ao cair:jump_upjump_down
    if state == JUMP and velocity.y > 0:
    $AnimationPlayer.play("jump_down")
    Mais tarde, uma vez que o nível é concluído, o jogador será passado um local de desova. Para lidar com isso, adicione esta função ao script:
    func reset(_position):
    position = _position
    show()
    change_state(IDLE)
    Com isso, você terminou de adicionar movimento, e a animação correta deve ser reproduzida para cada situação. Este seria um bom ponto para parar e testar o jogador para ter certeza de que tudo está funcionando. Você não pode simplesmente executar a cena, no entanto, porque o jogador vai começar a cair sem qualquer superfície para ficar.

Testando o movimento
Crie uma nova cena e adicione um objeto chamado (mais tarde, isso se tornará sua cena principal). Adicione uma ocorrência de , em seguida, adicione um nó com uma forma de colisão retangular. Estique a forma de colisão horizontalmente para que ela seja larga o suficiente para andar para frente e para trás e coloque-a abaixo do caractere:NodeMainPlayerStaticBody2D
![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/330e848a-af4c-45c3-87c7-cde22e4e416e)
Cena de teste com uma plataforma

Como ele não tem um nó, o corpo estático ficará invisível se você executar o jogo. No menu, escolha Depurar > Formas de Colisão Visíveis. Esta é uma configuração de depuração útil que desenhará as formas de colisão enquanto o jogo estiver em execução. Você pode ativá-lo sempre que precisar testar ou solucionar problemas de colisão.Sprite2D

Pressione Play Scene; Você deve ver o jogador parar de cair e executar a animação quando ele atinge o corpo estático.idle

Antes de seguir em frente, certifique-se de que todos os movimentos e animações estão funcionando corretamente. Execute e pule em todas as direções e verifique se as animações corretas estão sendo reproduzidas sempre que o estado muda. Se você encontrar algum problema, revise as seções anteriores e certifique-se de que não perdeu uma etapa.

Saúde do jogador
Eventualmente, o jogador vai encontrar perigo, então você deve adicionar um sistema de dano. O jogador começará com três corações e perderá um cada vez que forem danificados.

Adicione o seguinte à parte superior do script (logo após a linha de extensão):
signal life_changed
signal died
var life = 3: set = set_life
func set_life(value):
    life = value
    life_changed.emit(life)
    if life <= 0:
        change_state(DEAD)
        Você emitirá o sinal sempre que o valor for alterado, notificando a tela para atualizar. será emitido quando atingir 0.life_changedlifedeadlife

Adicione à função reset().life = 3

Há duas maneiras possíveis para o jogador se machucar: esbarrar em um objeto de espigão no ambiente ou ser atingido por um inimigo. Em ambos os casos, a seguinte função pode ser chamada:
func hurt():
    if state != HURT:
        change_state(HURT)
  Este código está sendo legal com o jogador: se ele já está machucado, ele não pode se machucar novamente (pelo menos por um breve período até que a animação pare de ser reproduzida). Sem isso, é fácil ficar preso em um loop de se machucar, resultando em uma morte rápida.hurt

Há algumas coisas a fazer quando o estado muda para na função criada anteriormente:HURTchange_state()
HURT:
    $AnimationPlayer.play("hurt")
    velocity.y = -200
    velocity.x = -100 * sign(velocity.x)
    life -= 1
    await get_tree().create_timer(0.5).timeout
    change_state(IDLE)
DEAD:
    died.emit()
    hide()
    HURT:
    $AnimationPlayer.play("hurt")
    velocity.y = -200
    velocity.x = -100 * sign(velocity.x)
    life -= 1
    await get_tree().create_timer(0.5).timeout
    change_state(IDLE)
DEAD:
    died.emit()
    hide()
    Quando eles são feridos, eles não só perdem um, mas também são saltados para cima e para longe do objeto danificado. Depois de um curto período de tempo, o estado muda de volta para IDLE.life

Além disso, a entrada deve ser desabilitada durante o estado. Adicione isso ao início de get_input():HURT
if state == HURT:
    return
    Agora, o jogador está pronto para levar dano uma vez que o resto do jogo foi configurado. Em seguida, você criará os objetos que o jogador coletará no jogo.
    Itens colecionáveis
Antes de começar a fazer o nível, você precisa criar alguns itens que o jogador pode coletar, já que esses também farão parte do nível. A pasta contém folhas de sprite para dois tipos de colecionáveis: cerejas e gemas.assets/sprites

Em vez de criar uma cena separada para cada tipo de item, você pode usar uma única cena e trocar a propriedade no script. Ambos os objetos têm o mesmo comportamento: animando no lugar e desaparecendo quando coletados pelo jogador. Você também pode adicionar um efeito para a coleção.texturetween
Configuração de cena
Inicie a nova cena com e nomeie-a . Salve a cena em uma nova pasta de itens.Area2DItem

Uma área é uma boa escolha para esses objetos porque você deseja detectar quando o jogador entra em contato com eles, mas não precisa de uma resposta de colisão deles. No Inspetor, defina Colisão/Camada como (camada 4) e Colisão/Máscara como (camada 2). Isso garantirá que apenas o nó será capaz de colecioná-los, enquanto os inimigos passarão direto.collectiblesplayerPlayer
dicione três nós filho: , e . Arraste para a Textura do nó. Defina HFrames como . Em seguida, adicione uma forma de círculo e dimensione-a adequadamente:Sprite2DCollisionShape2DAnimationPlayerres://assets/sprites/cherry.pngSprite2D5CollisionShape2D
![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/ae272f41-2ef3-440c-9297-a18f41ea2b57)
Item com colisão

Escolhendo um tamanho de colisão

Como regra geral, você deve dimensionar suas formas de colisão para que elas beneficiem o jogador. Isso significa que as hitboxes inimigas devem ser um pouco menores do que a imagem, enquanto as hitboxes de itens benéficos devem ser ligeiramente superdimensionadas. Isso reduz a frustração do jogador e resulta em uma melhor experiência de jogo.

Adicione uma nova animação a (você só precisa de uma, para que você possa nomeá-la como quiser). Defina Comprimento como segundos, Ajustar para segundos e Looping para ativado. Clique no botão Reprodução automática ao carregar para que a animação seja iniciada automaticamente.AnimationPlayer1.60.2

Defina a propriedade Frame do nó como e clique no botão de chave para criar a trilha. Essa folha de sprite contém apenas metade da animação, portanto, a animação precisa reproduzir os quadros na seguinte ordem:Sprite2D0

0 -> 1 -> 2 -> 3 -> 4 -> 3 -> 2 -> 1

Arraste o depurador para o tempo e digite Frame em . Em seguida, digite Frame at time . Defina o Modo de Atualização como Contínuo e pressione o botão Reproduzir. Você terá uma cereja bem animada! Observe que você também pode arrastar a imagem para o Texture e ele funcionará da mesma forma, já que tem o mesmo número de quadros. Isso facilitará a desova de cerejas e gemas em seu jogo.0.8411.4res://assets/sprites/coin.png

Script colecionável
O script precisa fazer duas coisas:Item
.Definir as condições de início (qual e posiçãotexture)
.Detectar quando o jogador se sobrepõe
Para a primeira parte, adicione o seguinte código ao novo script de item:
extends Area2D
signal picked_up
var textures = {
    "cherry": "res://assets/sprites/cherry.png",
    "gem": "res://assets/sprites/gem.png"
}
func init(type, _position):
    $Sprite2D.texture = load(textures[type])
    position = _position
    Você emitirá o sinal quando o jogador coletar o item. No dicionário, você encontrará uma lista dos tipos de itens e seus arquivos de imagem correspondentes. Observe que você pode colar rapidamente esses caminhos no script arrastando o arquivo do FileSystem e soltando-o no editor de scripts.picked_uptextures

Em seguida, os conjuntos de funções e . Seu script de nível usará isso para gerar todos os itens que você colocou em seu mapa de nível.init()textureposition

Finalmente, conecte o sinal de e adicione este código:body_enteredItem

func _on_item_body_entered(body):
    picked_up.emit()
    queue_free()
Explain
Esse sinal permitirá que o script principal do jogo reaja ao item que está sendo pego. Ele pode aumentar a pontuação, aumentar a saúde do jogador ou qualquer outro efeito que você queira que o item seja aplicado.

Você deve ter notado que esses itens colecionáveis são configurados muito parecidos com as moedas da Coin Dash. As áreas são muito úteis para qualquer tipo de item onde você precisa saber quando ele foi tocado. Na próxima seção, você começará a definir a cena de nível para que você possa colocar esses colecionáveis.
Projetando o nível
Para a maioria de vocês, esta seção ocupará a maior parte do seu tempo. Depois de começar a projetar um nível, você descobrirá que é muito divertido colocar todas as peças e criar saltos desafiadores, caminhos secretos e encontros perigosos.

Primeiro, você criará uma cena genérica contendo todos os nós e código que é comum a todos os níveis. Em seguida, você pode criar qualquer número de cenas que herdam desse nível mestre.LevelLevel

Usando TileMaps
Crie uma nova cena e adicione um nó chamado . Salve a cena em uma nova pasta chamada . É aqui que você salvará todos os níveis criados e todos eles herdarão a funcionalidade dessa cena. Todos eles terão a mesma hierarquia de nós – apenas o layout será diferente.Node2DLevelBaselevelslevel_base.tscn

Um mapa de blocos é uma ferramenta comum para projetar ambientes de jogo usando uma grade de blocos. Eles permitem que você desenhe um layout de nível pintando os blocos na grade em vez de colocar muitos nós individuais, um de cada vez. Eles também são mais eficientes porque agrupam todas as texturas de blocos individuais e formas de colisão em um único objeto de jogo.

Adicionar um nó; um novo painel TileMap aparecerá na parte inferior da janela do editor. Observe que ele diz que o TileMap editado não tem nenhum recurso TileSet.TileMap

Sobre TileSets
Para desenhar um mapa usando , ele deve ter atribuído. Ele contém todas as texturas individuais do bloco, juntamente com quaisquer formas de colisão que possam ter.TileMapTileSetTileSet

Dependendo de quantos blocos você pode ter, pode ser demorado para criar , especialmente pela primeira vez. Por esse motivo, alguns conjuntos de blocos pré-gerados foram incluídos na pasta. Sinta-se à vontade para usá-los, mas leia a seção a seguir. Ele contém informações úteis para ajudá-lo a entender como funciona. Se preferir usar os conjuntos de blocos fornecidos, vá para a seção Usando os conjuntos de blocos fornecidos .TileSetassetsTileSet

Criando um TileSet
Em Godot, é um tipo de . Exemplos de outros recursos incluem , e . Não são nós; Em vez disso, eles são contêineres que contêm um determinado tipo de dados e geralmente são salvos como arquivos TRES.TileSetResourceTextureAnimationRectangleShape2D.

Estas são as etapas para criar um contêiner TileSet:

Clique em Novo Conjunto de Blocos na área Conjunto de Blocos do . Você verá que agora tem um painel TileSet disponível, que pode ser selecionado na parte inferior da janela do editor. Você pode clicar nas duas setas para cima, , para fazer com que o painel preencha a tela do editor. Clique nele novamente para reduzir o painel.TileMap
A guia Blocos no lado esquerdo do painel é onde você pode colocar as texturas que deseja fatiar em blocos. Arraste para esta caixa. Um pop-up será exibido, perguntando se você deseja criar blocos automaticamente. Selecione Sim. Você verá que as caixas foram desenhadas ao redor de todos os blocos de pixels na imagem:TileSetres://assets/environment/tileset.png16x16
![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/20b598fd-a4ee-44d8-8e3e-114e53c518ac)
Adicionando um TileSet
ente selecionar o painel TileMap na parte inferior e, em seguida, selecione a imagem do bloco de grama no canto superior esquerdo dos blocos. Em seguida, clique na janela do editor para desenhar alguns blocos clicando com o botão esquerdo do mouse na janela do editor. Você pode clicar com o botão direito do mouse em um bloco para limpá-lo:
![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/c37a4c96-bed9-4fb7-a43e-39725c7d1c35)
Desenho com TileMaps

Se tudo o que você queria fazer era desenhar um fundo, você estaria pronto. No entanto, você também precisa adicionar colisões a esses blocos para que o jogador possa ficar sobre eles.

Abra o painel TileSet novamente e, no Inspetor, localize a propriedade PhysicsLayers e clique em Adicionar elemento:
![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/964cffd6-801b-4ac3-99d3-0d7775a29c6a)
Adicionando uma camada física ao TileSet

Como esses blocos estarão na camada, você não precisa alterar as configurações de camada/máscara.environment

Clique em Paint no painel TileSet e, em Propriedades do Paint, escolha Layer 0:Physics
![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/e4a38f3d-35dc-4abc-ae0e-d0008bc4fe52)
 Adicionando colisões a blocos

Comece a clicar nos blocos para adicionar a forma de colisão quadrada padrão a eles. Se você quiser editar a forma de colisão de um bloco, poderá fazê-lo – clique no bloco novamente para aplicar as alterações. Se você ficar preso a uma forma que não gosta, clique nos três pontos e escolha Redefinir para a forma de bloco padrão.
Você também pode arrastar a imagem para a lista de texturas para alguns itens decorativos para apimentar seu nível.props.png

Usando os TileSets fornecidos
Conjuntos de blocos pré-configurados foram incluídos no download deste projeto. Há três a serem adicionados a três nós TileMap diferentes :assets
World – : Telhas de chão e plataformatiles_world.tres
Items – : Marcadores para desova de colecionáveistiles_items.tres
Danger – : Itens que causam danos na colisãotiles_spikes.tres
Crie os mapas e tilemaps e adicione o conjunto de blocos associado à propriedade Tile Set.ItemsDanger

Adicione uma instância da cena e um nó chamado . Você pode usar este nó para marcar onde em seu nível você deseja que o jogador comece.PlayerMarker2DSpawnPoint

Anexe um script ao nó Nível:
extends Node2D
func _ready():
    $Items.hide()
    $Player.reset($SpawnPoint.position)
    Mais tarde, você estará escaneando o mapa para gerar colecionáveis nos locais designados. Essa camada de mapa não deve ser vista, então você pode defini-la como oculta na cena. No entanto, isso é fácil de esquecer, então garante que não seja visível durante o jogo.Items_ready()

Projetando o primeiro nível
Agora, você está pronto para começar a desenhar o nível! Clique em Cena > Nova Cena Herdada e escolha . Nomeie o nó raiz e salve-o (na pasta). Observe que os nós filho são coloridos de amarelo, indicando que são herdados do arquivo . Se você fizer alterações nessa cena original, essas alterações também aparecerão nessa cena.level_base.tscnLevel01levelslevel_base.tscn

Comece com o mapa e seja criativo. Você gosta de muitos saltos, ou túneis sinuosos para explorar? Corridas longas ou subidas cuidadosas?World

Antes de ir longe demais com seu design de nível, certifique-se de experimentar a distância de salto. Você pode alterar as propriedades do jogador para alterar a altura e a distância em que ele pode pular. Configure alguns tamanhos de intervalo diferentes e execute a cena para experimentá-los. Não se esqueça de arrastar o nó para o local onde você deseja que o jogador comece.jump_speedrun_speedgravitySpawnPoint

Como você define as propriedades de movimento do jogador terá um grande impacto em como seu nível deve ser definido. Certifique-se de que está satisfeito com as suas definições antes de gastar muito tempo com o design completo.

Depois de configurar o mapa, use o mapa para marcar os locais onde você gostaria de desovar as cerejas e gemas. Os azulejos que marcam os locais de desova são desenhados com um fundo magenta para que se destaquem. Lembre-se, eles serão substituídos em tempo de execução e os próprios blocos não serão vistos.WorldItems

Depois de ter seu nível definido, você pode limitar a rolagem horizontal da câmera do jogador para corresponder ao tamanho do mapa (além de um pequeno buffer em cada extremidade). Adicione este código ao level_base.gd:
func _ready():
    $Items.hide()
    $Player.reset($SpawnPoint.position)
    set_camera_limits()
func set_camera_limits():
    var map_size = $World.get_used_rect()
    var cell_size = $World.tile_set.tile_size
    $Player/Camera2D.limit_left = (map_size.position.x - 5)
        * cell_size.x
    $Player/Camera2D.limit_right = (map_size.end.x + 5) *
        cell_size.x
        O script também precisa escanear o mapa e procurar os marcadores de item. Coletar itens aumentará a pontuação do jogador, para que você possa adicionar uma variável para acompanhar isso também:Items
        signal score_changed
var item_scene = load("res://items/item.tscn")
var score = 0: set = set_score
func spawn_items():
    var item_cells = $Items.get_used_cells(0)
    for cell in item_cells:
        var data = $Items.get_cell_tile_data(0, cell)
        var type = data.get_custom_data("type")
        var item = item_scene.instantiate()
        add_child(item)
        item.init(type, $Items.map_to_local(cell))
        item.picked_up.connect(self._on_item_picked_up)
func _on_item_picked_up():
    score += 1
func set_score(value):
    score = value
    score_changed.emit(score)
    A função usa para obter uma lista de quais células não estão vazias. Essas células estão em coordenadas , não em pixels, portanto, mais tarde, quando você gerar o item, poderá usar para converter os valores.spawn_items()get_used_cells()TileMap_map coordinates_map_to_local()

Os blocos de marcador têm uma camada de dados personalizada anexada a eles (semelhante à camada de física que você adicionou aos blocos do mundo) que especifica que tipo eles são: ou . Isso é usado para dizer à nova instância que tipo de item deve ser.gemcherry

A variável está lá para acompanhar quantos itens o jogador colecionou. Você pode ter esse gatilho de conclusão do nível, dar um bônus, e assim por diante.score

Adicione e tente executar o nível. Você deve ver gemas e cerejas aparecerem onde quer que você as tenha adicionado. Além disso, verifique se eles desaparecem quando você os coleta.spawn_items()_ready()

Adicionando objetos perigosos
A camada de mapa é projetada para conter os objetos spike que prejudicarão o jogador quando tocado. Qualquer peça sobre isso causará danos ao jogador! Tente colocar alguns deles onde você pode facilmente testar a execução neles.DangerTileMap

Na guia Nó, adicione o mapa de blocos a um grupo chamado para que você possa identificá-lo facilmente ao colidir. Isso também permitirá que você crie outros objetos nocivos ao adicioná-los ao mesmo grupo.Dangerdanger

Sobre colisões de slides
Quando um nó é movido com o , ele pode colidir com mais de um objeto no mesmo movimento do quadro. Por exemplo, ao correr para um canto, o corpo pode bater na parede e no chão ao mesmo tempo. Você pode usar a função para descobrir quantas colisões ocorreram; Em seguida, você pode obter informações sobre cada colisão usando get_slide_collision().CharacterBody2Dmove_and_slide()get_slide_collision_count()

No caso do , você deseja detectar quando ocorre uma colisão contra o mapa de blocos. Você pode fazer isso logo após usar em player.gd:PlayerDangermove_and_slide()
if state == HURT:
    return
for i in get_slide_collision_count():
    var collision = get_slide_collision(i)
    if collision.get_collider().is_in_group("danger"):
        hurt()
        Observe que antes de verificar se há uma colisão com o grupo, você pode primeiro verificar se o jogador já está no estado. Se estiverem, você pode pular a verificação para ver se eles estão colidindo com um objeto perigoso.dangerHURT

O loop itera através do número de colisões dadas por para verificar cada uma delas para um objeto no grupo de perigo.forget_slide_collision_count()

Reproduza sua cena e tente se deparar com um dos picos. Você deve ver o player mudar para o estado (reproduzindo a animação) por um breve período antes de retornar ao . Após três acertos, o jogador entrará no estado, que atualmente só esconde o jogador.HURTIDLEDEAD

Plano de fundo de rolagem
Há duas imagens de plano de fundo na pasta: e , para o plano de fundo distante e próximo, respectivamente. Ao colocar essas imagens atrás do mapa de blocos e rolá-las em velocidades diferentes em relação à câmera, você pode criar uma ilusão atraente de profundidade em segundo plano:res://assets/environment/back.pngmiddle.png

Adicione um nó à cena (para que ele esteja presente em todos os níveis herdados). Este nó funciona com a câmera para criar um efeito de rolagem. Arraste esse nó para o topo da árvore de cena para que ele seja desenhado atrás do restante dos nós. Em seguida, adicione um nó como filho. pode ter qualquer número de filhos, permitindo que você faça várias camadas de rolagem independente.ParallaxBackgroundLevelBaseParallaxLayerParallaxBackgroundParallaxLayer
Adicione um nó como filho de e arraste a imagem para sua área Textura. Desmarque a propriedade Offset/Centered para que ela seja posicionada em relação à origem da tela. Também é um pouco pequeno, então defina a escala do nó como 1.5, 1.5).Sprite2DParallaxLayerback.pngSprite2D(
No , defina Movimento/Escala como (você precisará clicar no ícone de link para permitir que os valores e sejam definidos separadamente). Essa configuração controla a velocidade com que o plano de fundo rola em relação ao movimento da câmera. Ao defini-lo para um número menor que , a imagem só se moverá uma pequena quantidade à medida que o jogador se move para a esquerda e para a direita.ParallaxLayer(0.2, 1)xy1
Você precisa ter certeza de que a imagem se repete se seu nível for maior do que o tamanho da imagem, então defina Movimento/Espelhamento como . Esta é exatamente a largura da imagem ( vezes ), então a imagem será repetida quando tiver se movido por esse número de pixels.(576, 0)3841.5
Observe que essa imagem de plano de fundo foi projetada para níveis largos em vez de altos. Se você pular muito alto, verá a parte superior da imagem. Você pode corrigir isso definindo o limite superior da câmera. Se você não tiver movido a posição do plano de fundo, seu canto superior esquerdo ainda estará em (, para que você possa definir o limite superior da câmera como . Se você moveu ou seu nó, você pode encontrar o valor correto a ser usado observando o valor da posição do nó.0, 0)0ParallaxLayerSprite2Dy
Tente jogar o nível e correr para a esquerda e para a direita. Você deve ver o plano de fundo se movendo por uma pequena quantidade em comparação com a distância que você corre.
Adicione outro (também como filho de) e dê-lhe um filho. Desta vez, use a imagem. Esta imagem é muito mais estreita do que a imagem do céu, então você precisará ajustar algumas configurações para fazê-la repetir corretamente. Isso ocorre porque precisa ter imagens que sejam pelo menos tão grandes quanto a área do visor.ParallaxLayerParallaxBackgroundSprite2Dmiddle.pngParallaxBackground
Localize a propriedade Texture/Repeat do nó na seção CanvasItem e defina-a como . Em seguida, expanda a seção Região e marque a caixa Habilitado. Em Rect, defina a largura e a altura como . é a largura da imagem () multiplicada por , então agora você verá cinco repetições da imagem, cada uma um espelho da última.Sprite2DMirror(880, 368)8801765
Mova o nó para que a imagem se sobreponha à metade inferior da imagem do oceano/céu:Sprite2D

![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/c0d49a50-16a2-4a1c-94dd-aa95265ab967)
Configuração em segundo plano do Parallax

Defina Movimento/Escala do segundo nó como e Movimento/Espelhamento como (. Usar um fator de escala mais alto significa que essa camada rolará um pouco mais rápido do que a camada de nuvem atrás dela. Reproduza a cena para testar o efeito.ParallaxLayer(0.6, 1)880, 0)
A árvore de nós da sua cena agora deve ter a seguinte aparência:Level

![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/14ffa543-af8d-4bb3-8900-c412889f0d3f)
 Nós de cena de nível

Sua cena de nível agora tem todas as peças que você precisa para criar seu design de nível. Você quer que seu jogador tenha que fazer saltos muito precisos (um nível de parkour), correr por uma série de passagens sinuosas tentando encontrar todos os itens (um nível de labirinto), ou alguma combinação dos dois? Esta é a sua chance de experimentar algumas ideias criativas, mas certifique-se de deixar algum espaço para o próximo objeto que você fará: inimigos.
Adicionando inimigos
Há muitos comportamentos diferentes que você pode adicionar para um inimigo. Para este jogo, o inimigo caminhará ao longo de uma plataforma em linha reta e mudará de direção ao bater em um obstáculo.

Configuração de cena
Como antes, você precisará criar uma nova cena para representar o inimigo:

Comece com um nó chamado e dê a ele três filhos: , e AnimationPlayer.CharacterBody2DEnemySprite2DCollisionShape2D
Salve a cena em uma pasta chamada . Se você decidir adicionar mais tipos de inimigos ao jogo, você pode salvá-los todos aqui.enemies
Defina a Camada de colisão do corpo para inimigos e sua Máscara para ambiente, jogador e inimigos. Assim como acontece com o jogador, isso determina com quais tipos de objetos o inimigo irá colidir.
Também é útil agrupar inimigos, então clique na guia Nó e adicione o corpo a um grupo chamado inimigos.
Adicione à textura e defina Animação/Hframes como 6.res://assets/sprites/opossum.png
Adicione uma forma de colisão retangular que cubra a maior parte (mas não toda) da imagem, certificando-se de que a parte inferior da forma de colisão esteja alinhada com a parte inferior dos pés do gambá:

![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/cb1f4fd3-aa35-4ee4-8542-238d609a2c47)
 Forma de colisão inimiga

Adicione uma nova animação ao chamado . Defina Comprimento como segundos e ative Looping e Reprodução automática em Carregar.AnimationPlayerwalk0.6
A animação precisa ter duas faixas: uma que define a propriedade Texture do nó e outra que altera sua propriedade Frame. Clique no ícone de chave ao lado de Textura para adicionar a primeira faixa e, em seguida, adicione quadros-chave para Quadro no tempo zero e Quadro no tempo . Não se esqueça de alterar o Modo de Atualização para Contínuo.walkSprite2D050.5
Quando terminar, sua animação deve ter a seguinte aparência:

![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/88e0361d-b7aa-4767-b3a3-da01d80e6603)
Script do inimigo
A essa altura, mover um nó já é familiar para você. Olhe para este script e tente entender o que ele está fazendo antes de ler a explicação fornecida depois:CharacterBody2D
extends CharacterBody2D
@export var speed = 50
@export var gravity = 900
var facing = 1
func _physics_process(delta):
    velocity.y += gravity * delta
    velocity.x = facing * speed
    $Sprite2D.flip_h = velocity.x > 0
    move_and_slide()
    for i in get_slide_collision_count():
        var collision = get_slide_collision(i)
        if collision.get_collider().name == "Player":
            collision.get_collider().hurt()
        if collision.get_normal().x != 0:
            facing = sign(collision.get_normal().x)
            velocity.y = -100
    if position.y > 10000:
        queue_free()
        Nesse script, a variável acompanha o movimento na direção, ou . Tal como acontece com o jogador, depois de se mover, você deve verificar as colisões de slide. Se o objeto que colide for o jogador, você deve chamar sua função hurt().facingx1-1

Em seguida, você deve verificar se o vetor normal do corpo em colisão tem um componente que não é. Isso significa que ele aponta para a esquerda ou direita, o que significa que é uma parede ou outro obstáculo. A direção do normal é então usada para definir a nova face. Dar ao corpo uma pequena velocidade ascendente dará ao inimigo um pequeno efeito de salto ao se virar, o que parecerá mais atraente.x0

Por fim, se por algum motivo o inimigo cair de uma plataforma, você não quer que o jogo tenha que rastreá-lo caindo para sempre, então você deve excluir qualquer inimigo cuja coordenada se torne muito grande.y

Adicione uma instância de à sua cena de nível. Certifique-se de que tem alguns obstáculos em ambos os lados e tocar a cena. Verifique se o inimigo anda de um lado para o outro entre os obstáculos. Tente colocar o jogador em seu caminho e verifique se a função do jogador é chamada.Enemyhurt()

Você pode notar que se você pular em cima do inimigo, nada acontece. Vamos tratar dessa parte a seguir.

Danificando o inimigo
Não é justo se o jogador não consegue revidar, então, na tradição de Mario, pular em cima do inimigo irá derrotá-lo.

Comece adicionando uma nova animação ao nó do inimigo chamada . Defina Length como e Ajuste para . Não ative o looping para esta animação.AnimationPlayerdeath0.30.05

A animação também definirá Textura e Quadro. Arraste a imagem para a área Textura do sprite e clique na tecla para adicionar um quadro-chave para ela. Como antes, keyframe o e valores de Frame no início e no final da animação. Lembre-se de definir o Modo de Atualização como Contínuo.deathres://assets/sprites/enemy_death.png05

Adicione o seguinte código para que você tenha uma maneira de acionar a animação de morte no inimigo:enemy.gd
func take_damage():
    $AnimationPlayer.play("death")
    $CollisionShape2D.set_deferred("disabled", true)
    set_physics_process(false)
    uando o jogador acertar o inimigo nas condições certas, ele chamará , que reproduz a animação, desativa a colisão e interrompe o movimento.take_damage()death

Quando a animação de morte terminar de jogar, não há problema em remover o inimigo, então conecte o sinal do AnimationPlayer:animation_finished

![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/ee8769f5-8027-49a4-8389-d6c9349d6359)
Sinais do AnimationPlayer

Esse sinal é chamado toda vez que qualquer animação termina, então você precisa verificar se é o correto:

func _on_animation_player_animation_finished(anim_name):
    if anim_name == "death":
        queue_free()
        Para concluir esse processo, vá para o script e adicione o seguinte código à seção de onde você verifica as colisões. Este código irá verificar se o jogador acertou um inimigo de cima:player.gd_physics_process()
        for i in get_slide_collision_count():
    var collision = get_slide_collision(i)
    if collision.get_collider().is_in_group("danger"):
        hurt()
    if collision.get_collider().is_in_group("enemies"):
        if position.y < collision.get_collider().position.y:
            collision.get_collider().take_damage()
            velocity.y = -200
        else:
            hurt()
            Este código compara a posição dos pés do jogador com a posição do inimigo para ver se o jogador está acima do inimigo. Se forem, o inimigo deve ser ferido; caso contrário, o jogador deve estar.yy

Execute o nível novamente e tente pular no inimigo para verificar se tudo está funcionando conforme o esperado.

Script do jogador
Você fez várias adições ao roteiro do jogador. Veja como deve ser o script completo agora:
extends CharacterBody2D
signal life_changed
signal died
@export var gravity = 750
@export var run_speed = 150
@export var jump_speed = -300
enum {IDLE, RUN, JUMP, HURT, DEAD}
var state = IDLE
var life = 3: set = set_life
func _ready():
    change_state(IDLE)
func change_state(new_state):
    state = new_state
    match state:
        IDLE:
            $AnimationPlayer.play("idle")
        RUN:
            $AnimationPlayer.play("run")
        HURT:
            $AnimationPlayer.play("hurt")
            velocity.y = -200
            velocity.x = -100 * sign(velocity.x)
            life -= 1
            await get_tree().create_timer(0.5).timeout
            change_state(IDLE)
        JUMP:
            $AnimationPlayer.play("jump_up")
        DEAD:
            died.emit()
            hide()
func get_input():
    if state == HURT:
        return
    var right = Input.is_action_pressed("right")
    var left = Input.is_action_pressed("left")
    var jump = Input.is_action_just_pressed("jump")
    # movement occurs in all states
    velocity.x = 0
    if right:
        velocity.x += run_speed
        $Sprite2D.flip_h = false
    if left:
        velocity.x -= run_speed
        $Sprite2D.flip_h = true
    # only allow jumping when on the ground
    if jump and is_on_floor():
        change_state(JUMP)
        velocity.y = jump_speed
    # IDLE transitions to RUN when moving
    if state == IDLE and velocity.x != 0:
        change_state(RUN)
    # RUN transitions to IDLE when standing still
    if state == RUN and velocity.x == 0:
        change_state(IDLE)
    # transition to JUMP when in the air
    if state in [IDLE, RUN] and !is_on_floor():
        change_state(JUMP)
func _physics_process(delta):
    velocity.y += gravity * delta
    get_input()
    move_and_slide()
    if state == HURT:
        return
    for i in get_slide_collision_count():
        var collision = get_slide_collision(i)
        if collision.get_collider().is_in_group("danger"):
            hurt()
        if collision.get_collider().is_in_group("enemies"):
            if position.y <
            collision.get_collider().position.y:
                collision.get_collider().take_damage()
                velocity.y = -200
            else:
                hurt()
    if state == JUMP and is_on_floor():
        change_state(IDLE)
    if state == JUMP and velocity.y > 0:
        $AnimationPlayer.play("jump_down")
func reset(_position):
    position = _position
    show()
    change_state(IDLE)
    life = 3
func set_life(value):
    life = value
    life_changed.emit(life)
    if life <= 0:
        change_state(DEAD)
func hurt():
    if state != HURT:
        change_state(HURT)
        Se você estiver tendo algum problema com o código do player, tente pensar em qual parte pode ser o problema. É o movimento? A detecção de acerto ao se deparar com um inimigo? Se você puder reduzir o problema, isso o ajudará a determinar em qual parte do script você deve se concentrar.

Certifique-se de que está satisfeito com a forma como o jogador está a comportar-se antes de passar para a próxima secção.
Interface do usuário do jogo
Como nos projetos anteriores em que você trabalhou, você precisará de um HUD para exibir informações durante o jogo. Coletar itens aumentará a pontuação do jogador, de modo que esse número deve ser exibido, bem como o valor de vida restante do jogador, que será mostrado como uma série de corações.

Configuração de cena
Crie uma nova cena com um nó raiz nomeado e salve-a em uma nova pasta. Defina Layout como Largo Superior e, na seção Substituições/Constantes de Tema do Inspetor, defina as margens direita e esquerda como e as margens superior/inferior como 20.MarginContainerHUDui50

Adicione um nó para manter as coisas alinhadas e dê a ele dois filhos, e , named e LifeCounter, respectivamente.HBoxContainerLabelHBoxContainerScore

No rótulo, defina a propriedade Text como e no Inspetor, em Dimensionamento de layout/contêiner, marque a caixa Expandir. Em Configurações de Rótulo, adicione um novo objeto de configurações para configurar a fonte. Arraste para a propriedade Font e defina Size como . Em Contorno, defina Tamanho como e Cor como preto. Você deve ver exibido em branco com um contorno preto.Score100res://assets/Kenney Thick.ttf4816100

Para , adicione um filho e nomeie-o . Arraste para a área Textura e defina Modo de Alongamento para Manter o Aspecto Centralizado. Selecione e duplique (Ctrl + D) quatro vezes para que você tenha uma linha de cinco corações:LifeCounterTextureRectL1res://assets/heart.pngL1

![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/aabf1bdb-2c67-4220-aae7-195dcf9581fd)
Configuração do nó HUD

Quando terminar, seu HUD deverá ter a seguinte aparência:

![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/fc0ed7cf-a207-4d15-b16a-e72897d882d8)
Visualização do HUD

O próximo passo será adicionar um script para que o HUD possa atualizar durante o jogo.

Criando scripts do HUD
Esse script precisa de duas funções que podem ser chamadas para atualizar os dois valores que estão sendo exibidos:
extends MarginContainer
@onready var life_counter = $HBoxContainer/LifeCounter.get_children()
func update_life(value):
    for heart in life_counter.size():
        life_counter[heart].visible = value > heart
func update_score(value):
    $HBoxContainer/Score.text = str(value)
    Observe que, no , você calcula quantos corações exibir definindo se o número desse coração é menor do que a quantidade de vida.update_life()visiblefalse

Anexando o HUD
Abra (a cena de nível base, não a sua cena) e adicione . Adicione uma instância de como filho deste Canvaslayer.level_base.tscnLevel01CanvasLayerHUD

Selecione a instância do nível e conecte seu sinal ao método update_life() do HUD :Playerlife_changed

![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/afb7e0b0-5a28-4760-985f-07ead09b9a62)
 Ligar um sinal

Faça o mesmo com o sinal do nó, conectando-o ao método update_score() do HUD .score_changedLevel

Observe que se você não quiser usar a árvore de cena para conectar os sinais, ou se achar a janela de conexão de sinal confusa ou difícil de usar, você pode fazer a mesma coisa em seu script adicionando estas linhas à função de level.gd:_ready()

$Player.life_changed.connect($CanvasLayer/HUD.update_life)
score_changed.connect($CanvasLayer/HUD.update_score)

Copy

Explain
Jogue o jogo e verifique se você pode ver o HUD e se ele é atualizado corretamente. Certifique-se de coletar alguns itens e deixar o inimigo bater em você. Sua pontuação está aumentando? Quando você é atingido, você perde um coração? Depois de verificar isso, você pode continuar para a próxima seção e fazer a tela de título.
Tela de título
A tela de título é a primeira coisa que o jogador verá, e o jogo retornará a essa tela quando o jogador morrer e o jogo terminar.

Configuração de cena
Comece com um nó e defina Layout como Ret Completo. Adicione um nó usando a imagem. Defina o layout como Full Rect e Stretch Mode para manter o aspecto coberto.ControlTextureRectback.png

Adicione outro, desta vez usando e definindo o Modo de Alongamento como Bloco. Arraste a largura do retângulo até que ele fique mais largo que a tela e organize-o de modo que cubra a metade inferior.TextureRectmiddle.png

Adicione dois nós nomeados e defina suas propriedades Text como e , respectivamente. Adicione a fonte a cada um como você fez antes, definindo o título para tamanho e a mensagem para tamanho . Defina o layout do título como Centralizado e o layout da mensagem como Parte inferior central.LabelTitleMessageJungle JumpPress Space to Play7248

Quando terminar, a cena deve ter a seguinte aparência:

![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/f7c3a2f4-1faa-49f5-ae23-f2f8a7a9b9ee)
Tela de título

Para tornar a tela de título mais interessante, adicione um nó a ela. Crie uma nova animação chamada e defina-a para reprodução automática. Nesta animação, você pode animar os elementos da tela para fazê-los se mover, aparecer, fade in ou qualquer outro efeito que você gosta.AnimationPlayerintro

Por exemplo, quadro-chave a Posição atual de no momento . Em seguida, no momento, arraste para fora da parte superior da tela e adicione outro quadro-chave. Agora, o título cairá na tela quando você reproduzir a cena.Title0.50Title

Sinta-se à vontade para adicionar faixas que animam as propriedades dos outros nós. Por exemplo, aqui está uma animação que solta o título, desaparece nas duas texturas e, em seguida, faz com que a mensagem apareça:

![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/55c69dc0-4b57-4b42-b85b-bbec42180bc5)
Animações da tela de título

Esta tela de título foi mantida simples, mas você deve se sentir livre para adicionar a ela, se quiser. Você pode mostrar um exemplo de algumas plataformas, adicionar uma animação do personagem correndo pela tela ou alguma outra arte do jogo. Mas o que acontece quando o jogador acerta "start"? Para isso, você precisa carregar o primeiro nível na cena principal.
Configurando a cena principal
Você fez algumas cenas de nível, mas eventualmente, você vai querer fazer mais de uma. Como o jogo sabe qual carregar? Sua cena vai cuidar disso.Main

Exclua todos os nós extras que você adicionou quando estava testando o movimento do jogador. Essa cena agora será responsável por carregar o nível atual. Antes que ele possa fazer isso, no entanto, você precisa de uma maneira de acompanhar o nível atual. Você não pode acompanhar essa variável na cena de nível porque ela será substituída por um nível recém-carregado quando terminar. Para acompanhar os dados que precisam ser transportados de cena em cena, você pode usar um carregamento automático.main.tscn

Sobre carregamentos automáticos

Em Godot, você pode configurar um script ou cena como um carregamento automático. Isso significa que o motor irá carregá-lo automaticamente em todos os momentos. Mesmo se você alterar a cena atual no , o nó carregado automaticamente permanecerá. Você também pode se referir a essa cena carregada automaticamente pelo nome de qualquer outro nó do seu jogo.SceneTree

No editor de scripts, crie um novo script chamado e adicione o seguinte código:game_state.gd
extends Node
var num_levels = 2
var current_level = 0
var game_scene = "res://main.tscn"
var title_screen = "res://ui/title.tscn"
func restart():
    current_level = 0
    get_tree().change_scene_to_file(title_screen)
func next_level():
    current_level += 1
    if current_level <= num_levels:
        get_tree().change_scene_to_file(game_scene)
Você deve definir como o número de níveis que você criou na pasta. Certifique-se de nomeá-los consistentemente como , e assim por diante para que eles possam ser encontrados facilmente.num_levelslevelslevel_01.tscnlevel_02.tscn

Para adicionar esse script como um carregamento automático, abra Configurações do projeto e localize a guia Autoload. Clique no ícone de pasta para escolher e, em seguida, clique no botão Adicionar .game_state.gd

Em seguida, adicione este script à sua cena principal:
extends Node
func _ready():
    var level_num = str(GameState.current_level).pad_zeros(2)
    var path = "res://levels/level_%s.tscn" % level_num
    var level = load(path).instantiate()
    add_child(level)

    gora, sempre que a cena for carregada, ela incluirá a cena de nível que corresponde ao nível atual.Main

A tela de título precisa fazer a transição para a cena do jogo, portanto, anexe este script ao nó Título:
extends Control
func _input(event):
    if event.is_action_pressed("ui_select"):
        GameState.next_level()
        Finalmente, você pode chamar a função quando o jogador morrer, adicionando-a ao . Na cena, conecte o sinal de morte da instância :restart()level.gdLevelPlayer
        func _on_player_died():
    GameState.restart()
    Agora você deve ser capaz de jogar através do jogo completamente. Certifique-se de que está definido como a cena principal do jogo (ou seja, a que é executada primeiro). Se você já definiu uma cena diferente para ser a cena "principal", poderá alterar isso em Configurações de projetos em Aplicativo/Executar:title.tscn

    ![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/3866b02a-ac45-44ec-8313-5df5cee74edb) 
    Escolhendo uma cena principal
    Transição entre níveis
Seus níveis agora precisam de uma maneira de fazer a transição de um para o outro. Na folha de sprite, há uma imagem de uma porta que você pode usar para a saída do seu nível. Encontrar e entrar na porta levará o jogador para o próximo nível.res://assets/environment/props.png

Cena da porta
Faça uma nova cena com um nó nomeado e salve-a na pasta. Adicione um nó e use a imagem como Textura. Em Região, clique em Habilitado e clique no botão Editar Região para selecionar a imagem da porta na folha de sprite. Em seguida, em Offset/Offset, defina y como . Isso garantirá que, quando a porta for colocada no local da telha, ela será posicionada corretamente.Area2DDooritemsSprite2Dprops.png-8

Adicione um nó e dê-lhe uma forma retangular que cubra a porta. Coloque a porta na camada e defina sua máscara para que ela apenas escaneie a camada do jogador.CollisionShape2Ditems

Esta cena não precisa de um script porque você só vai usar seu sinal no script de nível.body_entered

Para colocar a porta no nível, você pode usar o objeto da porta do conjunto de azulejos, que você está usando em seu mapa de azulejos para colocar as cerejas e pedras preciosas. Coloque uma porta no seu nível e abra level.gd.tiles_itemsItems

Na parte superior do , defina a cena da porta:level.gd
var door_scene = load("res://items/door.tscn")
Em seguida, atualize para que ele também instancie portas:spawn_items()
func spawn_items():
    var item_cells = $Items.get_used_cells(0)
    for cell in item_cells:
        var data = $Items.get_cell_tile_data(0, cell)
        var type = data.get_custom_data("type")
        if type == "door":
            var door = door_scene.instantiate()
            add_child(door)
            door.position = $Items.map_to_local(cell)
            door.body_entered.connect(_on_door_entered)
        else:
            var item = item_scene.instantiate()
            add_child(item)
            item.init(type, $Items.map_to_local(cell))
            item.picked_up.connect(self._on_item_picked_up)
            Adicione a função que será chamada quando o jogador tocar na porta:
            func _on_door_entered(body):
            GameState.next_level()
            Jogue o jogo e tente entrar na porta. Se você tiver definido um número maior que 1, o jogo tentará carregar quando você tocar na porta.num_levelsgame_state.gdlevel_02.tscn

Configurações da tela
Lembre-se de que, no início deste capítulo, você define Stretch/Mode e Aspect em Configurações do projeto como e , respectivamente. Execute o jogo e, em seguida, tente redimensionar a janela do jogo. Observe que se você tornar a janela mais larga, você pode ver mais do mundo do jogo à esquerda/direita do jogador. É isso que o valor está fazendo.canvas_itemsexpandexpand

Se você quiser evitar isso, você pode configurá-lo para em vez disso, que sempre mostrará a mesma quantidade do mundo do jogo como mostrado pela câmera. No entanto, isso também significa que, se você fizer sua janela com uma forma diferente do jogo, você receberá barras pretas para preencher o espaço extra.keep

Como alternativa, a configuração não exibirá as barras pretas, mas o conteúdo do jogo será esticado para preencher o espaço, distorcendo a imagem.ignore

Reserve algum tempo para experimentar as várias configurações e decidir qual você prefere.

Retoques finais
Agora que você completou a estrutura principal do jogo e, com sorte, projetou alguns níveis para o jogador aproveitar, você pode considerar fazer algumas adições para melhorar a jogabilidade. Nesta seção, você encontrará mais alguns recursos sugeridos – adicione-os no estado em que se encontram ou ajuste-os ao seu gosto.

Efeitos sonoros
Como nos projetos anteriores, você pode adicionar efeitos de áudio e música para melhorar a experiência. No , você encontrará arquivos de áudio que você pode usar para diferentes eventos do jogo, como salto do jogador, acerto do inimigo e retirada de itens. Há também dois arquivos de música: para a tela de título e para a cena de nível.res://assets/audio/Intro ThemeGrasslands Theme

Adicioná-los ao jogo será deixado para você, mas aqui estão algumas dicas:

Você pode achar útil ajustar o volume de sons individuais. Isso pode ser definido com a propriedade Volume dB. Definir um valor negativo reduzirá o volume do som.
Você pode anexar a música à cena principal; essa música será usada para todos os níveis. Você também pode anexar música separada a níveis individuais se quiser definir um determinado humor.level.tscn
Seu primeiro pensamento pode ser colocar em cena para tocar o som do captador. No entanto, como o captador é excluído quando o jogador toca nele, isso não funcionará bem. Em vez disso, coloque o player de áudio na cena, já que é onde o resultado da captação é manipulado (aumentando a pontuação).AudioStreamPlayerItemLevel
Salto duplo
Os saltos duplos são um recurso popular de plataforma. O jogador recebe um segundo, geralmente menor, impulso para cima se pressionar a tecla de salto uma segunda vez enquanto estiver no ar. Para implementar esse recurso, você precisa adicionar algumas coisas ao script do jogador.

Primeiro, você precisará de variáveis para acompanhar o número de saltos e determinar o tamanho do segundo impulso:

@export var max_jumps = 2
@export var double_jump_factor = 1.5
var jump_count = 0



Explain
Ao entrar no estado, redefina o número de saltos:JUMP

JUMP:
    $AnimationPlayer.play("jump_up")
    jump_count =1

Explain
No , permita o salto se ele atender às condições que foram. Coloque isso antes da declaração onde você verifica se o jogador está no chão:get_input()if

if jump and state == JUMP and jump_count < max_jumps and jump_count > 0:
    $JumpSound.play()
    $AnimationPlayer.play("jump_up")
    velocity.y = jump_speed / double_jump_factor
    jump_count += 1


Explain
No , quando você pousar no chão, redefina a contagem de saltos:_physics_process()

if state == JUMP and is_on_floor():
    change_state(IDLE)
    jump_count =

Explain
Jogue o seu jogo e experimente os saltos duplos. Observe que esse código faz com que o segundo salto seja do tamanho da velocidade ascendente do salto inicial. Você pode ajustar isso de acordo com suas preferências.

Partículas de poeira
Gerar partículas de poeira nos pés do personagem é um efeito de baixo esforço que pode adicionar muito personagem aos movimentos do seu jogador. Nesta seção, você adicionará uma pequena baforada de poeira aos pés do jogador que é emitida sempre que eles pousam no chão. Isso adiciona uma sensação de peso e impacto aos saltos do jogador.

Adicione um nó à cena e nomeie-a . Defina as seguintes propriedades:CPUParticles2DPlayerDust

![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/ad231d52-0042-4904-8704-6538216e8e6a)
A cor padrão da partícula é branca, mas o efeito de poeira ficará melhor em um tom bronzeado. Ele também deve desaparecer para que pareça se dissipar. Isso pode ser feito com o . Na área Rampa Cor/Cor, selecione Novo Gradiente.Gradient

Gradient tem duas cores: uma cor inicial à esquerda e uma cor final à direita. Estes podem ser selecionados usando os pequenos retângulos em cada extremidade do gradiente. Clicar no quadrado grande à direita permite que você defina a cor para o retângulo selecionado:

![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/8973932b-0747-491a-9507-b24b4aa058bc)
Rampa de cores

Defina a cor inicial como um tom bronzeado e defina o final para a mesma cor, mas com o valor alfa definido como . Você deve ver um efeito de fumaça continuamente inchando. No Inspetor, coloque One Shot para on. Agora, as partículas só emitirão uma vez, cada vez que você marcar a caixa Emissora.0

Sinta-se livre para alterar as propriedades que foram fornecidas aqui. Experimentar efeitos de partículas pode ser muito divertido e, muitas vezes, você vai se deparar com um efeito muito bom apenas por mexer.

Quando estiver satisfeito com sua aparência, adicione o seguinte ao código _physics_process() do jogador :

if state == JUMP and is_on_floor():
    change_state(IDLE)
    $Dust.emitting = true


Explain
Execute o jogo e observe o sopro de poeira toda vez que seu personagem pousar no chão.

Escadas
A folha de sprite do jogador inclui quadros para uma animação de escalada, e o conjunto de azulejos contém imagens de escada. Atualmente, as telhas da escada não fazem nada – na , elas não têm nenhuma forma de colisão atribuída. Tudo bem porque você não quer que o jogador colida com as escadas – você quer que ele seja capaz de subir e descer sobre elas.TileSet

Código do jogador
Comece selecionando o nó do jogador e adicionando uma nova animação chamada . Seu comprimento deve ser e deve ser definido como loop. Os valores de Frame para são 0 -> 1 -> 0 -> 2.AnimationPlayerclimb0.4Sprite2D

Vá para e adicione um novo estado, , ao enum. Além disso, adicione duas novas declarações de variáveis na parte superior do script:player.gdCLIMBstate

@export var climb_speed = 50
var is_on_ladder = false



Explain
Você vai usar para acompanhar se o jogador está em uma escada ou não. Usando isso, você pode decidir se as ações para cima e para baixo devem ter algum efeito.is_on_ladder

No , adicione uma condição para o novo estado:change_state()

CLIMB:
    $AnimationPlayer.play("climb")



Explain
No , você precisa verificar as ações de entrada e, em seguida, determinar se elas alteram o estado:get_input()

var up = Input.is_action_pressed("climb")
var down = Input.is_action_pressed("crouch")
if up and state != CLIMB and is_on_ladder:
    change_state(CLIMB)
if state == CLIMB:
    if up:
        velocity.y = -climb_speed
        $AnimationPlayer.play("climb")
    elif down:
        velocity.y = climb_speed
        $AnimationPlayer.play("climb")
    else:
        velocity.y = 0
        $AnimationPlayer.stop()
if state == CLIMB and not is_on_ladder:
    change_state(IDLE)



Explain
Aqui, você tem três novas condições para verificar. Primeiro, se o jogador não está no estado, mas está em uma escada, então pressionar para cima deve fazer o jogador começar a subir. Em segundo lugar, se o jogador estiver subindo no momento, as entradas para cima e para baixo devem fazê-lo subir e descer a escada, mas parar a animação de jogar se nenhuma ação for pressionada. Finalmente, se o jogador deixar a escada durante a subida, ele deixa o estado CLIMB.CLIMB

Você também precisa se certificar de que a gravidade não puxe o jogador para baixo enquanto ele estiver em uma escada. Adicione uma condição ao código de gravidade em _physics_process():

if state != CLIMB:
    velocity.y += gravity * delta



Explain
Agora, o jogador está pronto para subir, o que significa que você pode adicionar algumas escadas ao seu nível.

Configuração de nível
Adicione um nó nomeado à cena, mas ainda não adicione uma forma de colisão a ela. Conecte seus sinais e defina sua camada de colisão como e Máscara como . Isso garante que apenas o jogador possa interagir com a escada. Estes sinais são como você vai deixar o jogador saber que eles estão ou não em uma escada:Area2DLaddersLevelbody_enteredbody_exiteditemsplayer

func _on_ladders_body_entered(body):
    body.is_on_ladder = true
func _on_ladders_body_exited(body):
    body.is_on_ladder = false



Explain
Agora, o nível precisa procurar por qualquer telha de escada e adicionar formas de colisão à área sempre que encontrar uma. Adicione a seguinte função e chame-a em _ready():Ladderslevel.gd

func create_ladders():
    var cells = $World.get_used_cells(0)
    for cell in cells:
        var data = $World.get_cell_tile_data(0, cell)
        if data.get_custom_data("special") == "ladder":
            var c = CollisionShape2D.new()
            $Ladders.add_child(c)
            c.position = $World.map_to_local(cell)
            var s = RectangleShape2D.new()
            s.size = Vector2(8, 16)
            c.shape = s



Explain
Observe que as formas de colisão que você está adicionando têm apenas pixels de largura. Se você fizer a forma com toda a largura da telha da escada, o jogador ainda parecerá que está subindo mesmo quando estiver pendurado na lateral, o que parece um pouco estranho.8

Experimente – vá para uma de suas cenas de nível e coloque algumas peças de escada em qualquer lugar que você gostaria em seu mapa de azulejos. Jogue a cena e tente subir as escadas.World

Observe que se você estiver no topo de uma escada e pisar nela, você cairá para baixo em vez de descer (embora pressionar para cima ao cair fará com que você pegue a escada). Se preferir fazer a transição automática para o estado de escalada, você pode adicionar um check-in de queda adicional _physics_process().

Plataformas móveis
As plataformas móveis são uma adição divertida ao seu kit de ferramentas de design de níveis. Nesta seção, você criará uma plataforma móvel que você pode colocar em qualquer lugar do seu nível e definir seu movimento e velocidade.

Comece com uma nova cena usando um nó e nomeie-a . Salve a cena e adicione como uma criança. Como a arte da sua plataforma é toda em folhas de sprite e elas já foram fatiadas em blocos e tiveram colisões adicionadas, isso tornará sua plataforma fácil de desenhar. Adicionar como conjunto de blocos. Você também precisará verificar a caixa Collision Animatable, que garantirá que as colisões funcionem corretamente, mesmo em movimento.Node2DMovingPlatformTileMaptiles_world.tres

Desenhe alguns blocos no , mas certifique-se de começar na origem, para que as coisas se alinhem de forma limpa. Estas telhas funcionam bem para uma plataforma flutuante:TileMap(0, 0)


![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/3736da91-b716-47fa-8e1b-896235809482)


Adicione um script ao nó raiz e comece com estas variáveis:

@export var offset = Vector2(320, 0)
@export var duration = 10.0

Explain
Isso permitirá que você defina a quantidade de movimento e velocidade. é relativo ao ponto de partida e, como é um nó, você pode ter plataformas que se movem horizontalmente, verticalmente ou diagonalmente. é medido em segundos e representa quanto tempo levará o ciclo completo.offsetVector2duration

A plataforma estará sempre em movimento, então você pode iniciar a animação em . Ele usará um método para animar a posição em duas etapas: da posição inicial para a posição de deslocamento e vice-versa:_ready()tween

func _ready():
    var tween = create_tween().set_process_mode(
        Tween.TWEEN_PROCESS_PHYSICS)
    tween.set_loops().set_parallel(false)
    tween.tween_property($TileMap, "position", offset,
        duration / 2.0).from_current()
    tween.tween_property($TileMap, "position",
        Vector2.ZERO, duration / 2.0)


Explain
Aqui estão algumas notas sobre o uso de interpolação:

Você precisa definir o modo de processo para que o movimento seja sincronizado com a física e o jogador seja capaz de colidir corretamente com a plataforma (ou seja, ficar sobre ela).
set_loops() diz para repetir uma vez terminado.tween
set_parallel(false) diz para executar as duas interpolações de propriedade sequencialmente em vez de ao mesmo tempo.tween
Você também pode experimentar outras curvas de interpolação. A adição, por exemplo, fará com que a plataforma desacelere nas extremidades do movimento para um visual mais natural. Tente experimentar os outros tipos de transição.tween.set_trans(Tween.TRANS_SINE)
Agora, você pode adicionar instâncias de à cena de nível. Para garantir que as coisas se alinhem corretamente, certifique-se de ativar o ajuste de grade:MovingPlatform


![image](https://github.com/TuppyPower/-Primeiro-Jogo-2d-com-Godot/assets/130563312/bc86e091-6202-4304-925d-fe89309faa96)

O valor padrão é , mas você pode alterá-lo clicando nos três pontos ao lado do ícone e escolhendo Configurar ajuste.(8, 8)

Quando você executar o jogo agora, você terá muito mais para interagir. As escadas e plataformas móveis oferecem muito mais possibilidades para seus designs de níveis. Mas não precisa parar por aí! Considerando tudo o que você fez neste capítulo, há muitos outros recursos que você ainda pode adicionar. A animação do jogador inclui uma animação "agachada" – e se os inimigos pudessem jogar coisas no jogador que poderiam ser abaixadas? Muitos jogos de plataforma incluem mecânicas de movimento adicionais, como deslizar por encostas, saltos de parede, mudar a gravidade e muito mais. Escolha um e veja se você pode adicioná-lo.


    

























