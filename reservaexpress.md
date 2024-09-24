# ReservaExpress: Aplicação de Reserva de Passagens de Ônibus

## Descrição da Aplicação

ReservaExpress é uma plataforma de reserva de passagens de ônibus baseada em microsserviços. A aplicação permite que os clientes realizem uma série de operações relacionadas à viagem, incluindo a reserva de assentos, a consulta da disponibilidade de passagens, a visualização de horários de ônibus e a gestão completa de suas reservas.

A aplicação é composta por múltiplos serviços interconectados que trabalham juntos para oferecer um serviço robusto e escalável. Cada serviço é projetado para operar de forma independente, mas interage de maneira harmoniosa para garantir a consistência e a integridade dos dados.

## Casos de Uso Iniciais

1. Registro de Usuário
   - O cliente se registra na plataforma fornecendo informações básicas.

2. Login de Usuário
   - O cliente faz login na plataforma usando suas credenciais.

3. Busca de Viagens
   - O cliente pesquisa viagens disponíveis com base em critérios como origem, destino e data.

4. Reserva de Assento
   - O cliente seleciona uma viagem e reserva um ou mais assentos.

5. Pagamento de Reserva
   - O cliente efetua o pagamento da reserva.

6. Visualização de Reservas
   - O cliente visualiza suas reservas ativas e histórico de viagens.

## Modelo de Banco de Dados

### Entidades Principais

1. Usuario
   - id: int (chave primária)
   - nome: varchar
   - email: varchar
   - senha: varchar (hash)
   - telefone: varchar
   - data_registro: datetime

2. Viagem
   - id: int (chave primária)
   - origem: varchar
   - destino: varchar
   - data_partida: datetime
   - data_chegada: datetime
   - preco: decimal
   - capacidade_total: int

3. Reserva
   - id: int (chave primária)
   - id_usuario: int (chave estrangeira -> Usuario)
   - id_viagem: int (chave estrangeira -> Viagem)
   - data_reserva: datetime
   - status: enum ('confirmada', 'pendente', 'cancelada')

4. Assento
   - id: int (chave primária)
   - id_viagem: int (chave estrangeira -> Viagem)
   - numero: int
   - status: enum ('disponível', 'reservado', 'ocupado')

5. Pagamento
   - id: int (chave primária)
   - id_reserva: int (chave estrangeira -> Reserva)
   - valor: decimal
   - data_pagamento: datetime
   - metodo_pagamento: varchar

### Relacionamentos

- Um Usuario pode ter várias Reservas (1:N)
- Uma Viagem pode ter várias Reservas (1:N)
- Uma Viagem tem vários Assentos (1:N)
- Uma Reserva tem um Pagamento (1:1)

### Notas sobre o Modelo

1. Este modelo é uma representação simplificada e pode necessitar de ajustes conforme a aplicação se desenvolve.
2. Considerações de segurança, como o armazenamento seguro de senhas, serão ser implementadas.

## Fluxos de Uso Detalhados

### 1. Visualização de Viagens

#### Caso de Sucesso: Busca e visualização de viagens disponíveis

1. O usuário acessa a página inicial da ReservaExpress.
2. O usuário seleciona a opção "Buscar Viagens".
3. O usuário preenche os campos de origem, destino e data desejada.
4. O usuário clica em "Buscar".
5. O sistema processa a busca e exibe uma lista de viagens disponíveis que correspondem aos critérios.
6. Para cada viagem, o sistema mostra informações como horário de partida, horário de chegada, preço e número de assentos disponíveis.
7. O usuário pode clicar em uma viagem específica para ver mais detalhes, como horário, preço e duração da viagem.

#### Caso de Falha: Nenhuma viagem encontrada

1. O usuário segue os passos 1-4 do caso de sucesso.
2. O sistema processa a busca e não encontra nenhuma viagem que corresponda aos critérios.
3. O sistema exibe uma mensagem informando que não há viagens disponíveis para os critérios selecionados.
4. O sistema sugere ao usuário que tente datas alternativas ou rotas similares.

### 2. Login de Usuário

#### Caso de Sucesso: Login bem-sucedido

1. O usuário acessa a página de login da ReservaExpress.
2. O usuário insere seu email e senha nos campos apropriados.
3. O usuário clica no botão "Entrar".
4. O sistema valida as credenciais do usuário.
5. O sistema autentica o usuário com sucesso.
6. O sistema redireciona o usuário para a página principal da sua conta.

#### Caso de Falha: Credenciais inválidas

1. O usuário segue os passos 1-3 do caso de sucesso.
2. O sistema tenta validar as credenciais do usuário.
3. O sistema detecta que o email ou a senha estão incorretos e  exibe uma mensagem de erro informando que as credenciais são inválidas.

### 3. Visualização de Reservas

#### Caso de Sucesso: Visualização das reservas do usuário

1. O usuário faz login no sistema (seguindo o fluxo de login bem-sucedido).
2. O usuário navega até a seção "Minhas Reservas".
3. O sistema recupera e exibe uma lista das reservas ativas do usuário.
4. Para cada reserva, o sistema mostra informações como destino, data da viagem, número do assento e status da reserva.
5. O usuário pode clicar em uma reserva específica para ver mais detalhes.

#### Caso de Falha: Nenhuma reserva encontrada

1. O usuário segue os passos 1-2 do caso de sucesso.
2. O sistema busca as reservas do usuário, mas não encontra nenhuma.
3. O sistema exibe uma mensagem informando que o usuário não possui reservas ativas no momento.
4. O sistema oferece um link ou botão para que o usuário possa buscar e fazer novas reservas.

### 4. Reserva de Viagem

#### Caso de Sucesso: Reserva bem-sucedida

1. O usuário realiza a busca e seleção de uma viagem (seguindo o fluxo de visualização de viagens).
2. O usuário seleciona a viagem desejada e clica em "Reservar".
3. O sistema exibe os detalhes da viagem e um mapa de assentos disponíveis.
4. O usuário seleciona um assento disponível.
5. O sistema marca temporariamente o assento como reservado.
6. O usuário confirma a seleção e é redirecionado para a página de pagamento.
7. O usuário seleciona o método de pagamento e insere as informações necessárias.
8. O sistema processa o pagamento e exibe uma confirmação.
9. O sistema envia um e-mail de confirmação ao usuário com os detalhes da reserva.

#### Caso de Falha: Tentativa de reserva com assento já ocupado

1. O usuário segue os passos 1-4 do caso de sucesso.
2. O sistema verifica a disponibilidade em tempo real e descobre que o assento foi reservado por outro usuário segundos antes.
3. O sistema exibe uma mensagem informando que o assento não está mais disponível.
4. O sistema atualiza o mapa de assentos, mostrando o assento como ocupado.
5. O usuário é convidado a selecionar outro assento disponível.

#### Caso de Falha: Tentativa de reserva com pagamento recusado

1. O usuário segue os passos 1-7 do caso de sucesso.
2. O sistema envia a solicitação de pagamento para processamento.
3. O provedor de pagamento recusa a transação (por exemplo, devido a fundos insuficientes).
4. O sistema informa ao usuário que o pagamento foi recusado.
5. O sistema oferece ao usuário a opção de tentar outro método de pagamento.
6. Se o usuário não completar o pagamento em um tempo determinado, o sistema libera o assento reservado.

### 5. Cancelamento de Reserva (OPCIONAL)

#### Caso de Sucesso: Cancelamento de reserva bem-sucedido

1. O usuário faz login no sistema.
2. O usuário navega até a seção "Minhas Reservas".
3. O sistema exibe uma lista das reservas ativas do usuário.
4. O usuário seleciona uma reserva específica.
5. O usuário clica na opção "Cancelar Reserva".
6. O sistema exibe um aviso sobre as políticas de cancelamento e reembolso.
7. O usuário confirma o cancelamento.
8. O sistema processa o cancelamento, atualizando o status da reserva para "cancelada".
9. O sistema libera o assento, tornando-o disponível para outras reservas.
10. Se aplicável, o sistema inicia o processo de reembolso.
11. O sistema envia um e-mail de confirmação do cancelamento ao usuário.
