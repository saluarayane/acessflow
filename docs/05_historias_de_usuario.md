# ✍️ Histórias de Usuário — AccessFlow

Documento de requisitos funcionais, histórias de usuário e critérios de aceitação em BDD do AccessFlow, organizado conforme as fases do Roadmap Estratégico.

## 🗺️ Roadmap

As histórias estão listadas na ordem de prioridade da planilha [priorizacao_backlog.xlsx](priorizacao_backlog.xlsx).

### 🟢 Fase 1 — MVP da Credencial Digital (Release 1)

| História | Persona | Prioridade | Pontos | Iteração |
| :--- | :--- | :---: | :---: | :---: |
| US 1 — Validar o primeiro embarque por NFC | Cynthia | Alta | 5 | 2 |
| US 2 — Validar embarque num ônibus lotado, sem sinal de internet | Lucas | Alta | 8 | 2 |
| US 3 — Validar embarque por QR Code, sem NFC no celular | Beatriz | Alta | 5 | 3 |
| US 8 — Cadastrar conta com perfil de estudante | Cynthia | Alta | 3 | 1 |
| US 9 — Visualizar e ativar cartão virtual com foto | Cynthia | Alta | 3 | 1 |
| US 10 — Cadastro com vínculo de vale-transporte corporativo | Lucas | Alta | 5 | 1 |
| US 11 — Cadastro e ativação de cartão virtual avulso | Beatriz | Alta | 3 | 3 |
| US 12 — Visualização do histórico de embarques no app | Cynthia | Alta | 2 | 3 |
| US 29 — Alternar automaticamente de NFC para QR Code | Beatriz | Alta | 2 | 3 |
| US 30 — Ver o resultado do embarque em tempo real | Lucas | Média | 2 | 1 |
| US 31 — Aceitar termos e consentimento de dados (LGPD) no cadastro | Cynthia | Média | 2 | 1 |

### 🟡 Fase 2 — Recarga, Saldo e Segurança (Release 2)

| História | Persona | Prioridade | Pontos | Iteração |
| :--- | :--- | :---: | :---: | :---: |
| US 14 — Realizar recarga instantânea de saldo via Pix | Cynthia | Alta | 8 | 4 |
| US 17 — Bloqueio remoto de segurança via web | Lucas | Alta | 5 | 4 |
| US 18 — Reemissão digital instantânea da credencial | Lucas | Alta | 5 | 5 |
| US 13 — Receber notificação automática de saldo baixo | Cynthia | Média | 3 | 5 |
| US 16 — Extrato detalhado com filtros de período | Cynthia | Média | 3 | 5 |
| US 15 — Recarga via cartão de crédito ou débito | Beatriz | Média | 5 | 5 |

### 🔵 Fase 3 — Painel Web Institucional e Monetização (Release 3)

| História | Persona | Prioridade | Pontos | Iteração |
| :--- | :--- | :---: | :---: | :---: |
| US 4 — Cadastrar, importar e bloquear usuários pelo painel | Ana | Alta | 5 | 6 |
| US 5 — Configurar o crédito mensal automático de vale-transporte | Ana | Alta | 8 | 6 |
| US 7 — Auditar logs de segurança e tentativas de fraude | Fernanda | Alta | 8 | 7 |
| US 6 — Conferir o extrato de comissão sobre as recargas do período | Roberto | Alta | 5 | 6 |
| US 23 — Fechar relatório financeiro mensal de vale-transporte | Ana | Média | 5 | 7 |
| US 24 — Acompanhar dashboard da operadora com volume de validações por linha | Roberto | Média | 8 | 8 |
| US 25 — Visualizar relatórios regulatórios agregados de mobilidade | Fernanda | Média | 5 | 8 |

**Convenções do backlog:**
* A **prioridade** de cada história segue o valor de negócio da planilha (A = Alta, B = Média). A **iteração** considera também risco e dependências: a US 31 tem valor B, mas entra na iteração 1 por ser pré-requisito legal dos cadastros, e a US 30 entra cedo porque a tela de resultado é usada por todas as validações.
* A numeração das histórias segue o identificador do backlog. Os números 19 a 22 e 26 a 28 não fazem parte do escopo desta versão.
* O **Id do passo** de cada história corresponde ao seu número somado a 103 (US 1 = 104, US 31 = 134).

------------------------------------------------------------------------

# 🔐 Modelo de Credencial e Validação

As histórias de validação (US 1, 2, 3, 29 e 30) e de segurança (US 17 e 18) seguem o mesmo modelo de credencial, descrito aqui uma única vez.

### Credencial no aparelho
1. Na ativação do cartão virtual, o app gera um par de chaves no armazenamento seguro do aparelho (Android Keystore ou Keychain no iOS). A chave privada nunca sai do celular.
2. O backend registra a chave pública e emite uma **autorização offline** assinada, contendo: ID da credencial, tipo de benefício, limite de viagens offline e validade de até 48 horas.
3. A autorização é renovada automaticamente sempre que o aparelho tem conexão.

### Token de embarque
1. Cada token é gerado **no próprio aparelho, no momento do uso**, e contém: ID da credencial, contador sequencial, data e hora e a assinatura feita com a chave privada.
2. O token NFC vale por poucos segundos e o QR Code dinâmico vale por até 30 segundos. Ambos são de uso único.
3. Como a geração é local, NFC e QR Code funcionam com ou sem internet.

### Limite offline
1. O limite de viagens offline é calculado no momento da última sincronização: saldo disponível dividido pela tarifa do benefício.
2. O app desconta cada viagem do limite localmente. Quando o limite acaba ou a autorização expira, o app deixa de gerar tokens e orienta o usuário a se conectar.
3. Na sincronização, as viagens offline são debitadas do saldo real. Se houver divergência (por exemplo, validações em dois validadores sem conexão), a diferença é registrada e a credencial fica suspensa até a regularização.

### Validador
1. O validador confere a assinatura do token com a chave pública da credencial e verifica a validade da autorização, sem precisar de internet.
2. Para evitar reuso, o validador guarda o último contador aceito de cada credencial e rejeita contadores repetidos ou menores.
3. O validador recebe periodicamente a **lista de bloqueio** de credenciais (US 17). Uma credencial bloqueada é rejeitada assim que a lista chega ao validador, e em no máximo 48 horas, quando sua autorização expira.

### Validador simulado (item técnico)
Como a equipe não tem acesso a catracas reais, o projeto inclui um **validador simulado**: um app Android que lê NFC em modo leitor e QR Code pela câmera, aplica as regras acima e envia as validações ao backend quando está online. Ele é um item técnico habilitador, sem pontuação de história, desenvolvido junto com as US 1 e 2 e usado nos testes e na demonstração.

### Base de elegíveis na Fase 1
Até a entrega da US 4 (Fase 3), as listas de estudantes e colaboradores elegíveis das instituições piloto são carregadas pela equipe a partir de planilhas (CSV) enviadas por elas. As US 8 e 10 consultam essa base.

------------------------------------------------------------------------

# 🟢 Fase 1 — MVP da Credencial Digital

## US 1 — Cynthia — Validar o primeiro embarque por NFC

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 1 — MVP da Credencial Digital
- **Jornada de Usuário:** Cynthia — Cadastrar-se, ativar o cartão virtual e validar o primeiro embarque sem o cartão físico
- **Passo:** Validar o primeiro embarque por NFC

### Geral
- **Produto:** AccessFlow
- **Título:** Validar o primeiro embarque por NFC
- **Narrativa:** Como Cynthia, estudante que depende do ônibus todos os dias, eu quero validar meu embarque aproximando o celular da catraca, para confirmar que o cartão virtual realmente funciona no lugar do físico.
- **Prioridade:** Alta | **Tipo:** Feature | **Coluna:** Em trabalho

### Detalhes
**Descrição Detalhada:** Esta história cobre o momento decisivo da jornada de Cynthia: a primeira validação real na catraca. O app deve emitir, via NFC (HCE), um token de curta duração gerado no momento da aproximação, conforme o Modelo de Credencial, exibir feedback visual e sonoro imediato de sucesso ou falha e registrar a viagem no histórico.

**Orientações de Tela:**
- Tela cheia com animação de "aguardando aproximação";
- Feedback verde, ícone e vibração em caso de sucesso;
- Feedback vermelho com mensagem clara em caso de falha, com os botões "Tentar novamente" e "Usar QR Code".

**Regras de Negócio:**
1. O token NFC é válido por poucos segundos e é de uso único;
2. Após duas falhas de leitura, aplicar a alternância para QR Code definida na US 29;
3. Registrar as tentativas com falha para as métricas de confiabilidade.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Validação bem-sucedida por NFC**
- **Dado** que estou com o cartão virtual ativo
- **Quando** aproximo o celular do leitor NFC da catraca
- **Então** devo ver a confirmação de sucesso em até 10 segundos
- **E** a viagem deve aparecer no meu histórico

**Cenário: Falha na leitura NFC**
- **Dado** que aproximei o celular do leitor
- **Quando** a leitura falha
- **Então** devo ver uma mensagem clara de erro
- **E** devo ver a opção de usar QR Code como alternativa

**Orientações para Implementação:** Usar HCE (Host Card Emulation) no Android; gerar e assinar o token no aparelho no momento da aproximação, nunca antes; testar a leitura com o validador simulado; registrar os eventos de analytics `nfc_tentativa`, `nfc_sucesso` e `nfc_falha`.

**Id do passo:** 104

## US 2 — Lucas — Validar embarque num ônibus lotado, sem sinal de internet

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 1 — MVP da Credencial Digital
- **Jornada de Usuário:** Lucas — Ativar o cartão virtual e confiar que a validação funciona mesmo sem sinal de internet
- **Passo:** Validar embarque num ônibus lotado, sem sinal de internet

### Geral
- **Produto:** AccessFlow
- **Título:** Validação offline em ônibus lotado
- **Narrativa:** Como Lucas, trabalhador que pega ônibus lotado, eu quero validar meu embarque mesmo sem internet, para não depender de sinal de celular no meio da viagem.
- **Prioridade:** Alta | **Tipo:** Feature | **Coluna:** Em trabalho

### Detalhes
**Descrição Detalhada:** O app mantém no aparelho a autorização offline criptografada descrita no Modelo de Credencial, renovada sempre que há conexão. Com ela, o app gera tokens de embarque mesmo totalmente offline. A sincronização com o backend acontece automaticamente quando a conexão é restabelecida.

**Orientações de Tela:**
- Indicador discreto de "modo offline ativo" no cartão virtual, com o número de viagens offline disponíveis;
- Tela de validação funcionando normalmente, sem exigir internet;
- Selo de "sincronizando" quando a conexão volta.

**Regras de Negócio:**
1. A autorização offline vale por até 48 horas sem conexão;
2. O número de viagens offline é limitado pelo saldo disponível na última sincronização, dividido pela tarifa do benefício;
3. Quando o limite acaba ou a autorização expira, o app não gera novos tokens e orienta o usuário a se conectar;
4. Ao voltar a conexão, sincronizar as viagens validadas offline em segundo plano.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Validação sem internet**
- **Dado** que meu celular está sem conexão com a internet
- **E** tenho autorização offline válida e viagens offline disponíveis
- **Quando** aproximo o celular do leitor NFC da catraca
- **Então** a validação deve ser concluída normalmente
- **E** a viagem deve ficar marcada como "pendente de sincronização"

**Cenário: Limite offline esgotado**
- **Dado** que estou sem conexão e já usei todas as viagens offline disponíveis
- **Quando** tento validar o embarque
- **Então** o app não deve gerar um novo token
- **E** devo ver uma orientação para me conectar e atualizar o saldo

**Cenário: Sincronização após reconexão**
- **Dado** que tenho viagens validadas offline pendentes
- **Quando** o celular recupera a conexão com a internet
- **Então** as viagens devem sincronizar automaticamente com o backend
- **E** o saldo exibido deve refletir o consumo real

**Orientações para Implementação:** Armazenar a autorização offline e a chave da credencial no armazenamento seguro do aparelho; usar uma fila local (offline queue) para a sincronização; registrar os eventos de analytics `validacao_offline`, `limite_offline_esgotado` e `sincronizacao_concluida`.

**Id do passo:** 105

## US 3 — Beatriz — Validar embarque por QR Code, sem NFC no celular

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 1 — MVP da Credencial Digital
- **Jornada de Usuário:** Beatriz — Usar o AccessFlow mesmo sem vínculo institucional e sem NFC
- **Passo:** Validar embarque por QR Code, já que o celular não tem NFC

### Geral
- **Produto:** AccessFlow
- **Título:** Validação por QR Code dinâmico
- **Narrativa:** Como Beatriz, usuária avulsa sem NFC no celular, eu quero validar meu embarque com um QR Code, para conseguir usar o AccessFlow mesmo com um aparelho mais simples.
- **Prioridade:** Alta | **Tipo:** Feature | **Coluna:** Em trabalho

### Detalhes
**Descrição Detalhada:** O app gera um QR Code dinâmico, válido por poucos segundos, que o leitor da catraca escaneia para validar o embarque. Essa modalidade não depende de NFC no celular do usuário, garante a inclusão de aparelhos mais simples e é a forma de validação no iPhone.

**Orientações de Tela:**
- Botão "Validar por QR Code" sempre visível na tela de validação;
- QR Code em tela cheia, com brilho máximo e contador regressivo de validade;
- Nova geração automática se o código expirar sem uso.

**Regras de Negócio:**
1. O QR Code vale por no máximo 30 segundos;
2. Um novo código é gerado a cada tentativa, nunca reaproveitando um código anterior;
3. A detecção de aparelho sem NFC e a priorização do QR Code seguem a US 29.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Geração e uso do QR Code dentro da validade**
- **Dado** que estou na tela de validação de embarque
- **Quando** toco em "Validar por QR Code"
- **Então** um QR Code deve ser gerado com validade de até 30 segundos
- **E**, ao ser escaneado pela catraca dentro desse tempo, a validação deve ser concluída

**Cenário: QR Code expirado**
- **Dado** que gerei um QR Code de validação
- **Quando** o tempo de validade expira sem uso
- **Então** o código deve ser invalidado automaticamente
- **E** um novo código deve poder ser gerado com um toque

**Orientações para Implementação:** Gerar o conteúdo do QR Code a partir de um token assinado no aparelho, conforme o Modelo de Credencial, o que permite o uso offline; testar a leitura pela câmera do validador simulado; registrar os eventos de analytics `qr_gerado`, `qr_validado` e `qr_expirado`.

**Id do passo:** 106

## US 8 — Cynthia — Cadastrar conta com perfil de estudante

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 1 — MVP da Credencial Digital
- **Jornada de Usuário:** Cynthia — Cadastrar-se, ativar o cartão virtual e validar o primeiro embarque sem o cartão físico
- **Passo:** Baixar o app e criar conta informando a matrícula da faculdade

### Geral
- **Produto:** AccessFlow
- **Título:** Cadastro de conta de estudante
- **Narrativa:** Como Cynthia, estudante universitária, eu quero criar minha conta no aplicativo informando minha matrícula e meu e-mail institucional, para vincular meu perfil ao benefício estudantil.
- **Prioridade:** Alta | **Tipo:** Feature | **Coluna:** Em trabalho

### Detalhes
**Descrição Detalhada:** A tela de cadastro solicita os dados básicos do estudante (nome completo, CPF, e-mail institucional, senha e número de matrícula), consulta a elegibilidade na base de elegíveis da instituição e encaminha o usuário para a próxima etapa. Os termos e consentimentos da US 31 são apresentados antes da conclusão.

**Orientações de Tela:**
- Formulário simples com campos validados em tempo real;
- Botão destacado de "Continuar";
- Opção de suporte caso a matrícula não seja localizada.

**Regras de Negócio:**
1. O CPF e o e-mail devem ser únicos no sistema;
2. A matrícula deve existir na lista de estudantes elegíveis enviada pela instituição de ensino (na Fase 1, carregada a partir de CSV);
3. Em caso de divergência, bloquear o avanço e informar o canal de suporte da secretaria.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Cadastro com matrícula elegível**
- **Dado** que informo meus dados pessoais e uma matrícula válida e cadastrada pela instituição
- **E** aceitei os termos e consentimentos obrigatórios
- **Quando** toco em "Continuar"
- **Então** minha conta deve ser criada com sucesso
- **E** devo ser direcionada para a etapa de foto e confirmação do cartão virtual

**Cenário: Matrícula não localizada**
- **Dado** que informo uma matrícula que não consta na base de elegíveis da instituição
- **Quando** toco em "Continuar"
- **Então** devo ver uma mensagem indicando que o vínculo não foi encontrado
- **E** devo receber orientações para entrar em contato com a secretaria acadêmica

**Orientações para Implementação:** Implementar a validação de CPF e do formato de e-mail no frontend; integrar a verificação de matrícula ao endpoint de validação de elegibilidade; registrar os eventos de analytics `cadastro_estudante_iniciado`, `cadastro_estudante_sucesso` e `cadastro_estudante_erro_matricula`.

**Id do passo:** 111

## US 9 — Cynthia — Visualizar e ativar cartão virtual com foto

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 1 — MVP da Credencial Digital
- **Jornada de Usuário:** Cynthia — Cadastrar-se, ativar o cartão virtual e validar o primeiro embarque sem o cartão físico
- **Passo:** Confirmar o vínculo e ativar o cartão virtual

### Geral
- **Produto:** AccessFlow
- **Título:** Exibição e ativação do cartão virtual
- **Narrativa:** Como Cynthia, estudante com conta criada, eu quero cadastrar minha foto e visualizar meu cartão virtual ativo, para ter certeza de que meu benefício está pronto para uso.
- **Prioridade:** Alta | **Tipo:** Feature | **Coluna:** Em trabalho

### Detalhes
**Descrição Detalhada:** A tela exibe o cartão virtual com nome do usuário, foto facial, ID ou matrícula, instituição de ensino associada, tipo de benefício (Estudantil), status "Ativo" e saldo disponível. A foto permite a conferência visual do titular por motorista ou fiscal em caso de dúvida sobre o benefício. Na ativação, o app gera a credencial do aparelho descrita no Modelo de Credencial.

**Orientações de Tela:**
- Cartão com aparência de documento de identificação digital;
- Botão para captura ou envio da foto de perfil, com guia de enquadramento facial;
- Selo de status em verde indicando "Ativo".

**Regras de Negócio:**
1. A foto passa por uma verificação básica de presença de rosto, sem reconhecimento facial nem comparação biométrica;
2. O cartão não pode ser ativado sem foto cadastrada e sem o consentimento específico da foto (US 31);
3. O status deve refletir a elegibilidade informada pela instituição, atualizada a cada sincronização.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Ativação com captura de foto válida**
- **Dado** que estou na etapa de ativação e envio uma foto facial nítida
- **Quando** confirmo os dados do meu perfil
- **Então** meu cartão virtual deve ser exibido com a foto, o nome, o tipo de benefício e o status "Ativo"

**Cenário: Cartão inativo ou suspenso**
- **Dado** que meu cadastro consta como suspenso na instituição
- **Quando** tento acessar a tela do cartão virtual
- **Então** o cartão deve exibir o status "Inativo" em destaque de aviso
- **E** a geração de códigos para validação deve ficar bloqueada

**Orientações para Implementação:** Comprimir a imagem antes do envio ao backend; armazenar a URL da imagem com token de autorização de leitura; registrar os eventos de analytics `cartao_foto_enviada` e `cartao_virtual_ativado`.

**Id do passo:** 112

## US 10 — Lucas — Cadastro com vínculo de vale-transporte corporativo

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 1 — MVP da Credencial Digital
- **Jornada de Usuário:** Lucas — Ativar o cartão virtual e confiar que a validação funciona mesmo sem sinal de internet
- **Passo:** Criar conta com vínculo empresarial

### Geral
- **Produto:** AccessFlow
- **Título:** Cadastro de conta corporativa (vale-transporte)
- **Narrativa:** Como Lucas, funcionário CLT, eu quero me cadastrar usando meu ID funcional ou CPF, para ativar o cartão virtual de vale-transporte fornecido pela empresa.
- **Prioridade:** Alta | **Tipo:** Feature | **Coluna:** Em trabalho

### Detalhes
**Descrição Detalhada:** Permite ao trabalhador vincular seu perfil à empresa empregadora informando o CPF ou o código funcional, carregando automaticamente o benefício de vale-transporte associado.

**Orientações de Tela:**
- Tela de onboarding corporativo;
- Campo para busca da empresa parceira e digitação do ID funcional ou CPF;
- Tela de confirmação com o valor do benefício e o nome da empresa.

**Regras de Negócio:**
1. O CPF informado deve constar no lote enviado pelo RH da empresa parceira (na Fase 1, carregado a partir de CSV);
2. Se o usuário tiver mais de um vínculo ativo (por exemplo, estudante e estagiária), os benefícios ficam na mesma conta, com um cartão virtual por benefício e um seletor de cartão na tela inicial.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Vínculo corporativo localizado**
- **Dado** que digito meu CPF cadastrado previamente pelo RH da empresa
- **Quando** confirmo o código de verificação recebido
- **Então** o app deve vincular minha conta à empresa correspondente
- **E** o cartão virtual corporativo deve ser ativado com o saldo de vale-transporte disponível

**Cenário: Vínculo corporativo pendente**
- **Dado** que o RH ainda não enviou meus dados ao sistema
- **Quando** informo meu CPF
- **Então** devo ver uma mensagem informando que a credencial corporativa está aguardando liberação do RH

**Orientações para Implementação:** Endpoint de consulta de elegibilidade por CPF e CNPJ da empresa; envio de código de verificação por SMS ou e-mail para confirmar o titular; registrar os eventos de analytics `cadastro_corporativo_sucesso` e `cadastro_corporativo_pendente`.

**Id do passo:** 113

## US 11 — Beatriz — Cadastro e ativação de cartão virtual avulso

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 1 — MVP da Credencial Digital
- **Jornada de Usuário:** Beatriz — Usar o AccessFlow mesmo sem vínculo institucional e sem NFC
- **Passo:** Criar conta como usuária avulsa, sem vínculo institucional

### Geral
- **Produto:** AccessFlow
- **Título:** Cadastro de perfil avulso sem vínculo institucional
- **Narrativa:** Como Beatriz, usuária avulsa do transporte público, eu quero me cadastrar no app de forma simples, sem vínculo com empresa ou escola, para usar o cartão virtual com recarga própria.
- **Prioridade:** Alta | **Tipo:** Feature | **Coluna:** Em trabalho

### Detalhes
**Descrição Detalhada:** Fluxo simplificado de cadastro que exige apenas nome, CPF, e-mail e senha para disponibilizar um cartão virtual na modalidade "Tarifa Inteira / Avulso".

**Orientações de Tela:**
- Opção "Não possuo vínculo institucional / Uso avulso" na tela inicial de cadastro;
- Formulário enxuto com nome, CPF, e-mail e senha;
- Confirmação imediata e redirecionamento para o cartão com saldo de R$ 0,00.

**Regras de Negócio:**
1. Não exigir número de matrícula nem validação patronal;
2. Vincular por padrão o perfil "Tarifa Comum / Avulso";
3. Permitir a solicitação posterior de vínculo institucional.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Cadastro avulso concluído**
- **Dado** que escolho a opção de cadastro avulso, sem instituição
- **Quando** preencho nome, CPF, e-mail e senha válidos e aceito os termos
- **Então** meu cartão virtual avulso deve ser gerado na hora
- **E** a interface deve sugerir a primeira recarga

**Cenário: CPF já cadastrado em outra modalidade**
- **Dado** que informo um CPF que já possui conta ativa no AccessFlow
- **Quando** tento concluir o cadastro avulso
- **Então** devo ser orientada a fazer login ou recuperar meu acesso

**Orientações para Implementação:** Simplificar as validações de backend no fluxo avulso; direcionar o usuário para a tela de primeira recarga após a conclusão; registrar o evento de analytics `cadastro_avulso_concluido`.

**Id do passo:** 114

## US 12 — Cynthia — Visualização do histórico de embarques no app

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 1 — MVP da Credencial Digital
- **Jornada de Usuário:** Cynthia — Cadastrar-se, ativar o cartão virtual e validar o primeiro embarque sem o cartão físico
- **Passo:** Confirmar no histórico que o embarque foi registrado

### Geral
- **Produto:** AccessFlow
- **Título:** Exibição do histórico recente de viagens no app
- **Narrativa:** Como Cynthia, usuária que acabou de passar na catraca, eu quero ver minha viagem na tela inicial do app, para ter certeza de que o débito do embarque está correto.
- **Prioridade:** Alta | **Tipo:** Feature | **Coluna:** Em trabalho

### Detalhes
**Descrição Detalhada:** A tela inicial exibe um card resumido com as últimas viagens, incluindo linha ou veículo, data, hora, valor debitado e método de validação (NFC ou QR Code).

**Orientações de Tela:**
- Card "Última atividade" na tela principal;
- Lista expansível com data e hora, linha do ônibus, modalidade e valor;
- Selo visual indicando "NFC" ou "QR Code".

**Regras de Negócio:**
1. Atualizar o histórico assim que a confirmação de validação for recebida;
2. Viagens validadas offline aparecem como "Pendente de sincronização" e passam a "Sincronizada" quando a internet volta, mantendo o horário real da validação.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Atualização de viagem validada online**
- **Dado** que acabei de passar na catraca via NFC com conexão ativa
- **Quando** abro a tela inicial do aplicativo
- **Então** devo ver o registro da viagem no topo da lista, com hora, linha e valor debitado

**Cenário: Viagem sincronizada do modo offline**
- **Dado** que realizei um embarque em modo offline
- **Quando** a conexão com a internet é restabelecida e o app sincroniza
- **Então** a viagem deve aparecer no histórico com o horário exato em que a validação offline ocorreu

**Orientações para Implementação:** Endpoint de listagem de transações em ordem decrescente de data e hora; cache local com Room (Android) ou Core Data (iOS) para exibição offline imediata; registrar o evento de analytics `historico_viagem_visualizado`.

**Id do passo:** 115

## US 29 — Beatriz — Alternar automaticamente de NFC para QR Code

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 1 — MVP da Credencial Digital
- **Jornada de Usuário:** Beatriz — Usar o AccessFlow mesmo sem vínculo institucional e sem NFC
- **Passo:** Validar embarque por QR Code, já que o celular não tem NFC

### Geral
- **Produto:** AccessFlow
- **Título:** Alternância automática entre NFC e QR Code
- **Narrativa:** Como Beatriz, usuária do AccessFlow, eu quero que o aplicativo escolha automaticamente a melhor forma de validação entre NFC e QR Code, para não precisar decidir manualmente como fazer meu embarque.
- **Prioridade:** Alta | **Tipo:** Feature | **Coluna:** Backlog

### Detalhes
**Descrição Detalhada:** O aplicativo identifica a disponibilidade de NFC no aparelho e escolhe automaticamente a modalidade de validação. Com NFC disponível e ativo, a validação prioriza o NFC. Se o aparelho não tiver NFC (incluindo o iPhone nesta versão), se o NFC estiver desligado ou se a leitura falhar duas vezes, o aplicativo direciona o usuário para o QR Code. Esta história concentra todas as regras de alternância usadas nas US 1 e 3.

**Orientações de Tela:**
- Não apresentar ao usuário uma pergunta técnica sobre a disponibilidade do NFC;
- Quando o NFC estiver indisponível, apresentar diretamente o QR Code;
- Quando o NFC estiver desligado, exibir a mensagem curta **"NFC desligado"**, com atalho para ativação quando disponível, mantendo o QR Code como alternativa;
- Permitir a alternância manual para QR Code quando o usuário preferir.

**Regras de Negócio:**
1. Se o aparelho não possuir NFC, direcionar diretamente para o QR Code;
2. Se o NFC estiver desligado, apresentar o QR Code e sugerir a ativação do NFC;
3. Após duas falhas de leitura NFC, priorizar o QR Code;
4. O usuário pode alternar manualmente para o QR Code a qualquer momento.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Aparelho sem NFC**
- **Dado** que meu celular não possui NFC
- **Quando** acesso a tela de validação de embarque
- **Então** devo ser direcionada automaticamente para a validação por QR Code

**Cenário: NFC desligado**
- **Dado** que meu celular possui NFC, mas a função está desligada
- **Quando** acesso a tela de validação
- **Então** devo ver o QR Code como alternativa imediata
- **E** devo receber uma orientação para ativar o NFC

**Cenário: Duas falhas de leitura NFC**
- **Dado** que tentei validar por NFC e a leitura falhou duas vezes
- **Quando** a segunda falha é registrada
- **Então** o app deve apresentar o QR Code como forma principal de validação

**Cenário: Alternância manual**
- **Dado** que estou na tela de validação
- **Quando** escolho manualmente a opção QR Code
- **Então** o aplicativo deve apresentar o QR Code para validação

**Orientações para Implementação:** Detectar a presença e o estado do NFC pelas APIs do sistema operacional; implementar a lógica de alternância entre NFC e QR Code em um único componente usado pela tela de validação; registrar os eventos de analytics `fallback_qr_automatico`, `nfc_indisponivel` e `alternancia_manual`.

**Id do passo:** 132

## US 30 — Lucas — Ver o resultado do embarque em tempo real

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 1 — MVP da Credencial Digital
- **Jornada de Usuário:** Lucas — Ativar o cartão virtual e confiar que a validação funciona mesmo sem sinal de internet
- **Passo:** Validar embarque num ônibus lotado, sem sinal de internet

### Geral
- **Produto:** AccessFlow
- **Título:** Resultado da validação de embarque em tempo real
- **Narrativa:** Como Lucas, usuário do AccessFlow, eu quero receber um resultado claro logo após a validação do embarque, para saber se minha passagem foi aprovada ou recusada.
- **Prioridade:** Média | **Tipo:** Feature | **Coluna:** Backlog

### Detalhes
**Descrição Detalhada:** Após qualquer tentativa de validação (NFC, QR Code ou modo offline), o aplicativo apresenta um resultado claro e inequívoco. Em caso de aprovação, confirma o embarque. Em caso de negativa, informa de forma simples o motivo, quando possível, e oferece uma ação para tentar novamente ou usar outra modalidade.

**Orientações de Tela:**
- Tela de aprovação com confirmação visual clara;
- Tela de negativa com motivo simplificado;
- Não usar apenas cores para comunicar o resultado: sempre apresentar ícone e texto;
- Em caso de negativa, oferecer as ações **"Tentar novamente"** e **"Usar QR Code"**, quando aplicável;
- Respeitar as configurações de som e vibração do dispositivo.

**Regras de Negócio:**
1. O resultado da validação deve ser apresentado em até 10 segundos, com meta média de até 5 segundos;
2. Motivos de negativa incluem: saldo ou limite offline insuficiente, benefício indisponível, credencial bloqueada, vínculo pendente ou ausência de resposta do leitor;
3. Em caso de ausência de resposta do leitor, apresentar a mensagem **"Sem resposta do leitor"**.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Embarque aprovado**
- **Dado** que realizei uma validação válida por NFC, QR Code ou modo offline
- **Quando** o sistema conclui a validação
- **Então** devo ver imediatamente a confirmação de embarque aprovado

**Cenário: Embarque negado**
- **Dado** que minha tentativa de validação não pode ser autorizada
- **Quando** o sistema conclui a análise
- **Então** devo ver uma mensagem informando que o embarque foi negado
- **E** devo receber uma explicação simples do motivo

**Cenário: Leitor sem resposta**
- **Dado** que iniciei uma validação de embarque
- **Quando** o leitor não responde dentro do tempo esperado
- **Então** devo ver a mensagem **"Sem resposta do leitor"**
- **E** devo poder tentar novamente ou usar uma alternativa disponível

**Orientações para Implementação:** Garantir contraste e tamanho de fonte adequados para leitura rápida; implementar estados distintos de aprovação, negativa e tempo esgotado; registrar os eventos de analytics `resultado_aprovado`, `resultado_negado` (com o motivo), `resultado_timeout` e `tempo_ate_resultado`.

**Id do passo:** 133

## US 31 — Cynthia — Aceitar termos e consentimento de dados (LGPD) no cadastro

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 1 — MVP da Credencial Digital
- **Jornada de Usuário:** Cynthia — Cadastrar-se, ativar o cartão virtual e validar o primeiro embarque sem o cartão físico
- **Passo:** Baixar o app e criar conta informando a matrícula da faculdade

### Geral
- **Produto:** AccessFlow
- **Título:** Termos de uso e consentimento de dados no cadastro
- **Narrativa:** Como Cynthia, estudante que está se cadastrando no AccessFlow, eu quero entender e aceitar claramente como meus dados e minha foto serão usados, para concluir meu cadastro de forma transparente.
- **Prioridade:** Média | **Tipo:** Feature (conformidade) | **Coluna:** Backlog

### Detalhes
**Descrição Detalhada:** Antes da conclusão do cadastro nas modalidades das US 8, 10 e 11, e antes do envio da foto previsto na US 9, o aplicativo apresenta os termos de uso e a política de privacidade em linguagem simples. O consentimento para uso da foto é apresentado de forma específica e separada, informando a finalidade: conferência visual do titular do benefício. Esta história é executada na iteração 1, junto com os cadastros que dependem dela.

**Orientações de Tela:**
- Resumo conciso dos termos e da política de privacidade;
- Acesso ao texto completo;
- Caixas de seleção separadas para os termos e a política e para o consentimento específico da foto;
- Botão **"Continuar"** desabilitado enquanto os consentimentos obrigatórios não forem aceitos.

**Regras de Negócio:**
1. O aceite dos termos e da política e o consentimento para uso da foto são registrados separadamente;
2. Registrar a versão do documento, a data e hora e o usuário de cada consentimento;
3. Para usuários menores de 18 anos, aplicar o fluxo de autorização do responsável definido na orientação jurídica do projeto;
4. A foto facial é tratada como dado pessoal e, quando aplicável, dado pessoal sensível; ela não é usada para reconhecimento facial;
5. Coletar somente os dados necessários e adotar medidas de proteção para seu armazenamento;
6. Na Fase 1, a revogação do consentimento é solicitada pelo suporte.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Aceite obrigatório dos termos**
- **Dado** que estou realizando meu cadastro
- **Quando** não aceitei os termos e a política obrigatórios
- **Então** não devo conseguir concluir o cadastro

**Cenário: Consentimento específico para a foto**
- **Dado** que o cadastro exige o envio de uma foto
- **Quando** ainda não aceitei o consentimento específico para uso da foto
- **Então** não devo conseguir concluir essa etapa

**Cenário: Usuário menor de idade**
- **Dado** que o usuário é menor de idade
- **Quando** o cadastro exige a autorização do responsável
- **Então** o sistema deve solicitar e registrar essa autorização conforme a regra aplicável

**Cenário: Registro do consentimento**
- **Dado** que aceitei os termos e consentimentos obrigatórios
- **Quando** concluo o cadastro
- **Então** o sistema deve registrar a versão dos documentos, a data e hora e o usuário associado

**Orientações para Implementação:** Criar uma estrutura de histórico de consentimentos com usuário, tipo de consentimento, versão, data e hora e status; integrar o fluxo aos cadastros das US 8, 10 e 11 e à captura de foto da US 9; registrar os eventos de analytics `consentimento_exibido`, `consentimento_aceito` e `consentimento_menor_de_idade`.

**Id do passo:** 134

------------------------------------------------------------------------

# 🟡 Fase 2 — Recarga, Saldo e Segurança

## US 14 — Cynthia — Realizar recarga instantânea de saldo via Pix

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 2 — Recarga, Saldo e Segurança
- **Jornada de Usuário:** Cynthia — Recarregar o saldo pelo próprio app e acompanhar o extrato sem depender de ponto físico
- **Passo:** Recarregar via Pix pelo app

### Geral
- **Produto:** AccessFlow
- **Título:** Recarga de saldo instantânea por Pix
- **Narrativa:** Como Cynthia, estudante que precisa recarregar o saldo rapidamente, eu quero pagar a recarga via Pix dentro do app, para ter meus créditos liberados em poucos segundos.
- **Prioridade:** Alta | **Tipo:** Feature | **Coluna:** Análise

### Detalhes
**Descrição Detalhada:** Tela de seleção do valor da recarga, com opções predefinidas ou valor livre, geração de uma cobrança Pix (QR Code do Pix e código "copia e cola") e atualização do saldo no app assim que o pagamento é confirmado por webhook.

**Orientações de Tela:**
- Botões com valores sugeridos (R$ 20, R$ 50, R$ 100) e campo de valor livre;
- Tela da cobrança com botão destacado "Copiar código Pix";
- Contador regressivo indicando a expiração da cobrança.

**Regras de Negócio:**
1. Valor mínimo de recarga de R$ 5,00;
2. A cobrança Pix vale por até 15 minutos;
3. O saldo é atualizado automaticamente assim que o provedor de pagamentos (PSP) confirmar o pagamento via webhook;
4. Após a confirmação, a autorização offline é renovada com o novo limite de viagens.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Pagamento Pix com sucesso e crédito em tempo real**
- **Dado** que escolho o valor de R$ 20,00 e gero a cobrança Pix no app
- **Quando** realizo o pagamento no meu aplicativo bancário
- **Então** o AccessFlow deve receber o webhook de confirmação
- **E** exibir uma tela de sucesso, com R$ 20,00 somados ao saldo do cartão virtual em menos de 10 segundos

**Cenário: Cobrança Pix expirada sem pagamento**
- **Dado** que gerei uma cobrança Pix e não paguei dentro de 15 minutos
- **Quando** o prazo expira
- **Então** a cobrança deve ser cancelada no app
- **E** devo ver um aviso para gerar uma nova cobrança caso ainda queira recarregar

**Orientações para Implementação:** Integração com o gateway de pagamentos via API Pix (webhooks no backend e WebSockets no app para atualizar sem recarregar a tela); registrar os eventos de analytics `recarga_pix_gerada`, `recarga_pix_sucesso` e `recarga_pix_expirada`.

**Id do passo:** 117

## US 17 — Lucas — Bloqueio remoto de segurança via web

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 2 — Recarga, Saldo e Segurança
- **Jornada de Usuário:** Lucas — Perder o celular, bloquear o cartão virtual remotamente e conseguir uma reemissão digital sem burocracia
- **Passo:** Bloquear o cartão remotamente por outro dispositivo

### Geral
- **Produto:** AccessFlow
- **Título:** Bloqueio remoto imediato de credencial (perda ou roubo)
- **Narrativa:** Como Lucas, trabalhador que teve o celular roubado ou perdido, eu quero acessar o Portal Web do Usuário por outro aparelho e bloquear meu cartão virtual na hora, para impedir o uso indevido do meu saldo.
- **Prioridade:** Alta | **Tipo:** Security | **Coluna:** Análise

### Detalhes
**Descrição Detalhada:** Portal web de autoatendimento (adaptado a celular) para o usuário final, com login seguro, visualização dos cartões ativos e acionamento do bloqueio de emergência em caso de perda, roubo ou furto do smartphone. O Portal Web do Usuário é uma entrega da Fase 2, separada do painel institucional da Fase 3.

**Orientações de Tela:**
- Botão destacado em vermelho "Bloquear cartão por perda ou roubo";
- Caixa de confirmação exigindo a senha novamente;
- Tela de confirmação do bloqueio com data, hora e número de protocolo.

**Regras de Negócio:**
1. O bloqueio revoga a credencial do aparelho no backend e inclui o ID da credencial na lista de bloqueio enviada aos validadores, conforme o Modelo de Credencial;
2. A credencial passa para o status "Bloqueado por segurança";
3. O saldo fica preservado para a reemissão (US 18).

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Bloqueio remoto com confirmação de senha**
- **Dado** que acessei minha conta pelo portal web em outro dispositivo após perder o celular
- **Quando** seleciono o cartão e confirmo a ação "Bloquear por perda ou roubo" informando minha senha
- **Então** o status do cartão virtual deve mudar para "Bloqueado" imediatamente
- **E** a credencial deve ser incluída na lista de bloqueio dos validadores

**Cenário: Validação tentada com cartão bloqueado**
- **Dado** que o cartão de um usuário foi bloqueado remotamente
- **E** o validador já recebeu a lista de bloqueio atualizada
- **Quando** o aparelho antigo tenta validar um embarque
- **Então** o validador deve negar o embarque com o motivo "credencial bloqueada"

**Orientações para Implementação:** Invalidar as sessões JWT/OAuth do usuário e revogar a chave da credencial associada ao aparelho; publicar a lista de bloqueio para os validadores (no projeto, o validador simulado); enviar e-mail de alerta sobre o bloqueio realizado; registrar o evento de analytics `cartao_bloqueado_remotamente`.

**Id do passo:** 120

## US 18 — Lucas — Reemissão digital instantânea da credencial

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 2 — Recarga, Saldo e Segurança
- **Jornada de Usuário:** Lucas — Perder o celular, bloquear o cartão virtual remotamente e conseguir uma reemissão digital sem burocracia
- **Passo:** Solicitar a reemissão digital do cartão virtual

### Geral
- **Produto:** AccessFlow
- **Título:** Reemissão digital do cartão virtual
- **Narrativa:** Como Lucas, que perdeu o celular onde estava meu cartão virtual, eu quero reemitir a credencial no novo aparelho em poucos minutos, para voltar a usar meu vale-transporte sem pagar a tarifa do próprio bolso enquanto espero uma segunda via.
- **Prioridade:** Alta | **Tipo:** Feature | **Coluna:** Análise

### Detalhes
**Descrição Detalhada:** No modelo de cartão físico, a segunda via pode levar até 2 meses, e nesse período o usuário paga a tarifa sem o benefício. Esta história resolve essa dor: no novo aparelho, o app identifica a credencial bloqueada e transfere o benefício e o saldo de forma 100% digital, substituindo um processo de meses por uma ação de minutos.

**Orientações de Tela:**
- Tela de boas-vindas no novo celular informando "Você tem uma credencial bloqueada";
- Botão "Reemitir cartão digital";
- Etapa de verificação de identidade por código enviado ao e-mail cadastrado e confirmação de dados pessoais;
- Tela final com o novo cartão ativo e o saldo preservado.

**Regras de Negócio:**
1. Transferir integralmente o saldo remanescente e os benefícios ativos (meia-passagem ou VT) da credencial antiga para a nova;
2. A verificação de identidade usa o e-mail cadastrado e a confirmação de dados (CPF e data de nascimento). O SMS só é aceito se o número já estiver ativo no novo chip, já que o celular perdido pode estar com o chip original;
3. Desvincular o identificador do aparelho anterior, registrar o novo aparelho e gerar uma nova credencial conforme o Modelo de Credencial.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Reemissão com sucesso no novo celular**
- **Dado** que fiz login no aplicativo em um smartphone novo e possuo um cartão bloqueado
- **Quando** confirmo a verificação de segurança e solicito a reemissão
- **Então** uma nova credencial digital deve ser gerada, vinculada ao novo aparelho
- **E** o benefício tarifário e o saldo anterior devem estar integralmente disponíveis no novo cartão

**Cenário: Tentativa de reemissão sem confirmação de segurança**
- **Dado** que iniciei a reemissão no novo aparelho
- **Quando** erro a verificação de segurança (código incorreto ou dados divergentes)
- **Então** a reemissão não deve ser concluída
- **E** a credencial deve permanecer com o status "Bloqueado"

**Orientações para Implementação:** Endpoint de provisionamento de nova credencial vinculada ao novo `device_id`; migração atômica do saldo no banco de dados; registrar o evento de analytics `cartao_reemitido_digitalmente`.

**Id do passo:** 121

## US 13 — Cynthia — Receber notificação automática de saldo baixo

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 2 — Recarga, Saldo e Segurança
- **Jornada de Usuário:** Cynthia — Recarregar o saldo pelo próprio app e acompanhar o extrato sem depender de ponto físico
- **Passo:** Receber o alerta de saldo baixo

### Geral
- **Produto:** AccessFlow
- **Título:** Alerta e notificação push de saldo baixo
- **Narrativa:** Como Cynthia, usuária do aplicativo, eu quero receber uma notificação automática quando meu saldo atingir um limite mínimo de passagens, para recarregar antes de ir ao ponto de embarque.
- **Prioridade:** Média | **Tipo:** Feature | **Coluna:** Análise

### Detalhes
**Descrição Detalhada:** O sistema monitora o saldo do cartão virtual e dispara uma notificação push, além de um destaque na tela principal, quando o saldo for equivalente a 2 passagens ou menos. O limite pode ser ajustado pelo usuário.

**Orientações de Tela:**
- Notificação push no sistema operacional ("Seu saldo está baixo: resta 1 passagem.");
- Destaque em amarelo ou laranja no card de saldo do app;
- Ajuste do limite e ativação ou desativação do alerta em Perfil > Notificações.

**Regras de Negócio:**
1. O número de passagens é calculado com a tarifa do benefício do usuário (inteira, meia ou VT);
2. O alerta dispara após uma validação que reduza o saldo a um valor igual ou menor que o limite definido;
3. Não enviar notificações duplicadas para o mesmo evento de saldo baixo até que ocorra uma nova recarga.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Disparo de notificação após viagem**
- **Dado** que meu saldo atual equivale a 2 passagens (por exemplo, R$ 9,00 com tarifa de R$ 4,50)
- **Quando** realizo uma viagem que reduz o saldo para 1 passagem
- **Então** recebo uma notificação push alertando sobre o saldo baixo
- **E** vejo o card de saldo destacado na cor de alerta ao abrir o app

**Cenário: Limite ajustado pelo usuário**
- **Dado** que alterei o limite do alerta para 4 passagens em Perfil > Notificações
- **Quando** uma viagem reduz meu saldo para 4 passagens
- **Então** devo receber a notificação de saldo baixo

**Cenário: Saldo restabelecido por recarga**
- **Dado** que estou com o alerta de saldo baixo ativo
- **Quando** faço uma recarga que eleva o saldo acima do limite mínimo
- **Então** o alerta do card de saldo deve ser removido
- **E** o gatilho de notificação deve ser rearmado para as próximas viagens

**Orientações para Implementação:** Worker de verificação após cada débito no backend; integração com Firebase Cloud Messaging (FCM) e APNs; registrar o evento de analytics `alerta_saldo_baixo_disparado`.

**Id do passo:** 116

## US 16 — Cynthia — Extrato detalhado com filtros de período

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 2 — Recarga, Saldo e Segurança
- **Jornada de Usuário:** Cynthia — Recarregar o saldo pelo próprio app e acompanhar o extrato sem depender de ponto físico
- **Passo:** Conferir o extrato de viagens da semana

### Geral
- **Produto:** AccessFlow
- **Título:** Consulta de extrato detalhado com filtros
- **Narrativa:** Como Cynthia, usuária do app, eu quero filtrar meu extrato por período (semana, mês) e por tipo (recargas, viagens), para entender meus hábitos e controlar meus gastos com transporte.
- **Prioridade:** Média | **Tipo:** Feature | **Coluna:** Análise

### Detalhes
**Descrição Detalhada:** Tela de extrato com visão clara das entradas (recargas Pix, cartão e VT) em verde e das saídas (validações de embarque) em vermelho, com seletores de período flexíveis.

**Orientações de Tela:**
- Cabeçalho com saldo atual e totais de entradas e saídas do período selecionado;
- Abas de filtro "Tudo", "Viagens" e "Recargas";
- Seletor de período (Últimos 7 dias, Mês atual, Personalizado).

**Regras de Negócio:**
1. Exibir o histórico dos últimos 12 meses;
2. O detalhe de cada item mostra a linha (para viagens), o ID da transação e o método utilizado.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Filtragem por viagens nos últimos 7 dias**
- **Dado** que estou na tela de extrato e seleciono a aba "Viagens" e o período "Últimos 7 dias"
- **Quando** o filtro é aplicado
- **Então** a lista deve exibir apenas os débitos dos embarques da última semana
- **E** o total de saídas deve corresponder à soma exata dos valores exibidos

**Cenário: Extrato sem movimentação no período**
- **Dado** que seleciono um período em que não fiz viagens nem recargas
- **Quando** a consulta é realizada
- **Então** a tela deve exibir a mensagem "Nenhuma movimentação encontrada neste período"

**Orientações para Implementação:** Paginação dos resultados via API REST no backend; índices por `usuario_id` e `data_transacao`; registrar os eventos de analytics `extrato_consultado` e `extrato_filtro_aplicado`.

**Id do passo:** 119

## US 15 — Beatriz — Recarga via cartão de crédito ou débito

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 2 — Recarga, Saldo e Segurança
- **Jornada de Usuário:** Beatriz — Configurar notificações automáticas e entender seu padrão de uso pelo extrato
- **Passo:** Recarregar um valor pequeno via cartão de crédito

### Geral
- **Produto:** AccessFlow
- **Título:** Recarga de saldo com cartão de crédito ou débito
- **Narrativa:** Como Beatriz, usuária avulsa, eu quero cadastrar meu cartão de crédito ou débito, para fazer recargas rápidas sem sair do app.
- **Prioridade:** Média | **Tipo:** Feature | **Coluna:** Análise

### Detalhes
**Descrição Detalhada:** Permite incluir cartões de crédito e débito com armazenamento seguro por tokenização, identificação da bandeira, confirmação por CVV ou 3D Secure e opção de salvar o cartão para as próximas recargas.

**Orientações de Tela:**
- Formulário de pagamento com máscara automática nos campos;
- Opção "Salvar cartão para as próximas recargas";
- Botão de confirmação "Pagar R$ XX,XX".

**Regras de Negócio:**
1. Não armazenar dados sensíveis do cartão (número completo e CVV) no banco de dados da aplicação, usando tokenização conforme o PCI-DSS;
2. Exigir CVV ou 3D Secure em cada transação, inclusive com cartão salvo;
3. Após a aprovação, a autorização offline é renovada com o novo limite de viagens.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Recarga aprovada com cartão salvo**
- **Dado** que selecionei um cartão de crédito salvo anteriormente e digitei o CVV correto
- **Quando** confirmo a compra do valor selecionado
- **Então** a transação deve ser processada junto à adquirente
- **E** o valor aprovado deve ser somado imediatamente ao saldo do cartão virtual

**Cenário: Transação recusada pelo banco**
- **Dado** que tento recarregar com um cartão sem limite ou recusado pelo banco
- **Quando** o pagamento é processado
- **Então** devo ver uma mensagem clara informando a recusa do cartão
- **E** o saldo do meu cartão virtual deve permanecer inalterado

**Orientações para Implementação:** Integração com o SDK ou API do gateway de pagamento com suporte a tokenização e 3D Secure 2.0; registrar os eventos de analytics `recarga_cartao_sucesso` e `recarga_cartao_recusada`.

**Id do passo:** 118

------------------------------------------------------------------------

# 🔵 Fase 3 — Painel Web Institucional e Monetização

## US 4 — Ana — Cadastrar, importar e bloquear usuários pelo painel

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 3 — Painel Web Institucional e Monetização
- **Jornada de Usuário:** Ana — Gerenciar os colaboradores pelo painel, configurar o crédito automático mensal e integrar o AccessFlow ao sistema de RH
- **Passo:** Importar os colaboradores elegíveis e gerenciar as credenciais

### Geral
- **Produto:** AccessFlow
- **Título:** Gestão de usuários elegíveis no painel institucional
- **Narrativa:** Como Ana, analista de RH, eu quero importar a lista de colaboradores elegíveis e bloquear a credencial de quem foi desligado direto pelo painel, para resolver cadastros e desligamentos sem abrir chamado com a operadora.
- **Prioridade:** Alta | **Tipo:** Feature | **Coluna:** Backlog

### Detalhes
**Descrição Detalhada:** O painel permite cadastrar, editar, importar em lote (planilha CSV) e bloquear ou desbloquear usuários elegíveis da instituição. O mesmo módulo atende empresas (colaboradores com VT) e secretarias acadêmicas (estudantes matriculados). A lista importada passa a ser a base de elegíveis consultada pelas US 8 e 10, substituindo a carga manual feita pela equipe na Fase 1.

**Orientações de Tela:**
- Tela "Colaboradores" (ou "Estudantes", para instituições de ensino) com busca, filtros por status e ações por linha;
- Botão "Importar planilha" com modelo CSV para download e resumo da importação (novos, atualizados e com erro);
- Ação "Bloquear credencial" com campo de motivo (desligamento, evasão, suspensão).

**Regras de Negócio:**
1. Cada instituição vê e altera apenas os próprios usuários;
2. Linhas com CPF inválido ou duplicado são rejeitadas individualmente, sem cancelar a importação inteira;
3. O bloqueio feito pelo gestor segue o mesmo fluxo de revogação da US 17 e é registrado na trilha de auditoria (US 7);
4. Usuários removidos da lista de elegíveis em uma nova importação ficam com o benefício suspenso, e o gestor é avisado antes da confirmação.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Importação de lista de colaboradores**
- **Dado** que estou na tela "Colaboradores" do painel
- **Quando** envio uma planilha CSV no modelo fornecido
- **Então** devo ver o resumo da importação com novos cadastros, atualizações e linhas com erro
- **E** os colaboradores importados devem passar a constar como elegíveis no cadastro do app

**Cenário: Bloqueio no desligamento**
- **Dado** que um colaborador foi desligado da empresa
- **Quando** seleciono "Bloquear credencial" e informo o motivo "desligamento"
- **Então** a credencial do colaborador deve passar para o status "Bloqueado" imediatamente
- **E** a ação deve aparecer na trilha de auditoria com data, hora e responsável

**Orientações para Implementação:** Processar a importação de forma assíncrona, com validação linha a linha; reaproveitar o serviço de revogação de credencial da US 17; registrar os eventos de analytics `importacao_elegiveis_concluida`, `credencial_bloqueada_gestor` e `credencial_desbloqueada_gestor`.

**Id do passo:** 107

## US 5 — Ana — Configurar o crédito mensal automático de vale-transporte

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 3 — Painel Web Institucional e Monetização
- **Jornada de Usuário:** Ana — Gerenciar os colaboradores pelo painel, configurar o crédito automático mensal e integrar o AccessFlow ao sistema de RH
- **Passo:** Configurar o crédito mensal automático de vale-transporte

### Geral
- **Produto:** AccessFlow
- **Título:** Crédito automático mensal de benefício
- **Narrativa:** Como Ana, analista de RH, eu quero configurar o crédito mensal automático de vale-transporte por cargo, para eliminar o processo manual que se repete todo mês.
- **Prioridade:** Alta | **Tipo:** Feature | **Coluna:** Análise

### Detalhes
**Descrição Detalhada:** O painel permite definir regras de crédito automático (valor e data) associadas a cargos ou grupos de funcionários. O crédito recorrente é processado sem intervenção manual, o gestor recebe a confirmação da execução e cada colaborador é avisado no app quando o crédito cai.

**Orientações de Tela:**
- Tela "Crédito Automático" com a lista de regras por cargo ou grupo;
- Campos de valor e data de crédito;
- Chave para ativar ou pausar cada regra;
- Histórico dos créditos já processados.

**Regras de Negócio:**
1. Processar o crédito automaticamente na data configurada, mesmo sem login do gestor;
2. Não permitir duas regras ativas conflitantes para o mesmo grupo;
3. Notificar o gestor em caso de falha no processamento (por exemplo, cartão virtual não ativado);
4. Enviar uma notificação push ao colaborador quando o crédito for processado, com o valor creditado.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Execução do crédito automático na data configurada**
- **Dado** que configurei uma regra de crédito mensal para um grupo de funcionários
- **Quando** chega a data configurada
- **Então** o crédito deve ser processado automaticamente para todos os funcionários elegíveis
- **E** devo receber a confirmação de que o processamento foi concluído

**Cenário: Colaborador avisado do crédito**
- **Dado** que o crédito mensal de um colaborador foi processado
- **Quando** o valor é somado ao saldo do cartão virtual
- **Então** o colaborador deve receber uma notificação push informando o valor creditado

**Cenário: Falha no crédito de um funcionário específico**
- **Dado** que o crédito automático está sendo processado
- **Quando** um funcionário ainda não ativou o cartão virtual
- **Então** o crédito dele deve ficar pendente, sem travar o processamento dos demais
- **E** devo ver esse caso sinalizado no painel

**Orientações para Implementação:** Usar um job agendado (scheduler) para processar os créditos na data configurada; processar em lote, com tratamento individual de falhas; reaproveitar a infraestrutura de notificações da US 13; registrar os eventos de analytics `credito_automatico_processado`, `credito_pendente` e `credito_notificado_colaborador`.

**Id do passo:** 108

## US 7 — Fernanda — Auditar logs de segurança e tentativas de fraude

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 3 — Painel Web Institucional e Monetização
- **Jornada de Usuário:** Fernanda — Usar o painel institucional para fiscalizar a conformidade do AccessFlow em escala
- **Passo:** Auditar logs de segurança e tentativas de fraude

### Geral
- **Produto:** AccessFlow
- **Título:** Auditoria de segurança e conformidade
- **Narrativa:** Como Fernanda, representante de um consórcio de mobilidade urbana, eu quero auditar os logs de segurança do AccessFlow, para confirmar que o sistema é seguro o suficiente para operar em escala na cidade.
- **Prioridade:** Alta | **Tipo:** Feature | **Coluna:** Backlog

### Detalhes
**Descrição Detalhada:** O painel oferece uma visão consolidada dos eventos de segurança: tentativas de validação inválida, tokens repetidos rejeitados, bloqueios acionados por usuários e gestores, reemissões, divergências de saldo após sincronização offline e padrões suspeitos (por exemplo, o mesmo cartão validado em dois lugares distantes em curto intervalo), com filtros por período, operadora e tipo de evento.

**Orientações de Tela:**
- Tela "Logs de Auditoria" com filtros (período, operadora, tipo de evento);
- Lista de eventos com severidade sinalizada por cor, ícone e texto;
- Opção de exportar o relatório completo para documentação formal.

**Regras de Negócio:**
1. Sinalizar automaticamente padrões suspeitos (por exemplo, validações geograficamente incompatíveis no mesmo intervalo de tempo) para revisão;
2. Reter os logs de auditoria pelo período mínimo definido em contrato;
3. O acesso a este painel é restrito a perfis de auditoria e regulação.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Consulta de logs por período**
- **Dado** que estou na tela "Logs de Auditoria"
- **Quando** filtro por um período e uma operadora específicos
- **Então** devo ver todos os eventos de segurança relevantes daquele recorte

**Cenário: Sinalização de padrão suspeito**
- **Dado** que o sistema identifica duas validações do mesmo cartão em locais incompatíveis num curto intervalo
- **Quando** esse evento ocorre
- **Então** ele deve aparecer destacado como "suspeito" na tela de auditoria
- **E** deve ser possível abrir o detalhe do caso para investigação

**Orientações para Implementação:** Implementar a detecção de anomalia geográfica e temporal como job assíncrono; manter a trilha de auditoria imutável (append-only) para validade probatória; registrar os eventos de analytics `log_auditoria_consultado` e `evento_suspeito_sinalizado`.

**Id do passo:** 110

## US 6 — Roberto — Conferir o extrato de comissão sobre as recargas do período

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 3 — Painel Web Institucional e Monetização
- **Jornada de Usuário:** Roberto — Acompanhar a adoção do AccessFlow e validar o contrato de comissão sobre recargas
- **Passo:** Conferir o extrato de recargas e comissão do período

### Geral
- **Produto:** AccessFlow
- **Título:** Extrato financeiro de recargas e comissão da operadora
- **Narrativa:** Como Roberto, gestor de operadora de transporte, eu quero ver as recargas atribuídas à minha frota, a comissão retida pelo AccessFlow e o valor repassado, para validar se o modelo de receita compensa o contrato.
- **Prioridade:** Alta | **Tipo:** Feature | **Coluna:** Backlog

### Detalhes
**Descrição Detalhada:** O painel apresenta, por período, o total de recargas atribuídas à operadora, a comissão retida pelo AccessFlow, o valor líquido repassado e a projeção com base na tendência do período anterior, com exportação para conferência financeira.

**Orientações de Tela:**
- Gráfico de recargas, comissão e repasse por período;
- Tabela com detalhamento por linha e por data;
- Botão "Exportar extrato" (CSV ou PDF);
- Comparação com o período anterior.

**Regras de Negócio:**
1. O valor das recargas é atribuído às operadoras proporcionalmente às validações registradas em cada frota no período;
2. A comissão é calculada sobre o valor atribuído, conforme o percentual do contrato;
3. Os valores aparecem em tempo real, com fechamento oficial mensal;
4. O extrato exportado deve bater com o relatório usado para faturamento.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Consulta do extrato do período**
- **Dado** que estou no painel financeiro da operadora
- **Quando** seleciono um período específico
- **Então** devo ver o total de recargas atribuídas à minha frota, a comissão retida e o valor líquido repassado

**Cenário: Exportação do extrato**
- **Dado** que estou visualizando o extrato de um período
- **Quando** clico em "Exportar extrato"
- **Então** devo receber um arquivo com os mesmos valores exibidos na tela, pronto para conferência financeira

**Orientações para Implementação:** Calcular a atribuição e a comissão em job assíncrono diário, com cache para consulta rápida no painel; garantir que o valor exportado seja idêntico ao exibido (mesma fonte de dados); registrar os eventos de analytics `extrato_comissao_consultado` e `extrato_exportado`.

**Id do passo:** 109

## US 23 — Ana — Fechar relatório financeiro mensal de vale-transporte

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 3 — Painel Web Institucional e Monetização
- **Jornada de Usuário:** Ana — Gerenciar os colaboradores pelo painel, configurar o crédito automático mensal e integrar o AccessFlow ao sistema de RH
- **Passo:** Fechar o relatório de custo do mês para o financeiro

### Geral
- **Produto:** AccessFlow
- **Título:** Fechamento e relatório financeiro de vale-transporte
- **Narrativa:** Como Ana, analista de RH, eu quero exportar o demonstrativo dos custos mensais com vale-transporte por centro de custo ou departamento, para enviar ao setor financeiro até o dia 5 de cada mês.
- **Prioridade:** Média | **Tipo:** Feature | **Coluna:** Backlog

### Detalhes
**Descrição Detalhada:** Módulo de consolidação que apresenta o total creditado nas contas dos colaboradores, os saldos não utilizados e a memória de cálculo por centro de custo ou departamento no período.

**Orientações de Tela:**
- Tela "Fechamento Mensal VT";
- Resumo com valor total creditado, taxa de serviço e valor líquido;
- Gráfico de gastos por departamento;
- Botão "Fechar mês e exportar para o financeiro".

**Regras de Negócio:**
1. O fechamento mensal bloqueia alterações em períodos já encerrados, garantindo a consistência contábil;
2. Os valores consolidados devem ser idênticos aos da nota fiscal ou fatura emitida.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Geração do fechamento mensal**
- **Dado** que estou no painel e seleciono o mês de referência a ser encerrado
- **Quando** clico em "Gerar fechamento financeiro"
- **Então** o sistema deve exibir o detalhamento de custos por departamento e o valor consolidado final
- **E** permitir o download da memória de cálculo em Excel e PDF

**Cenário: Visualização de mês já encerrado**
- **Dado** que um mês já foi encerrado pelo gestor
- **Quando** acesso os dados desse período
- **Então** a tela deve exibir os valores somente para leitura, com o selo "Período encerrado"

**Orientações para Implementação:** Rotina de fechamento com congelamento dos registros do período (snapshot financeiro); geração de relatórios em PDF e XLSX por biblioteca dedicada; registrar o evento de analytics `fechamento_financeiro_concluido`.

**Id do passo:** 126

## US 24 — Roberto — Acompanhar dashboard da operadora com volume de validações por linha

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 3 — Painel Web Institucional e Monetização
- **Jornada de Usuário:** Roberto — Acompanhar a adoção do AccessFlow e validar o contrato de comissão sobre recargas
- **Passo:** Acessar o painel da operadora e ver o volume de validações por linha

### Geral
- **Produto:** AccessFlow
- **Título:** Dashboard operacional por linha e frota
- **Narrativa:** Como Roberto, gestor de operadora de transporte, eu quero acompanhar em tempo real o volume de validações em cada linha e veículo da frota, para monitorar a operação e avaliar o desempenho da bilhetagem digital.
- **Prioridade:** Média | **Tipo:** Feature | **Coluna:** Backlog

### Detalhes
**Descrição Detalhada:** Painel para operadoras de transporte com o volume de passageiros que validaram pelo AccessFlow, separado por linha de ônibus, horário, modalidade (NFC ou QR Code, online ou offline) e veículo, além da média diária de uso, das recargas e do saldo médio.

**Orientações de Tela:**
- Tabela e gráficos de volume de embarques por linha;
- Mapa de calor dos horários de maior fluxo de validações;
- Seletor de período (Hoje, Últimos 7 dias, Mês).

**Regras de Negócio:**
1. O painel exibe apenas os dados dos veículos e linhas da concessão daquela operadora;
2. Os dados de validação online são atualizados várias vezes por hora; as validações offline entram assim que o validador sincroniza.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Embarques por linha em tempo real**
- **Dado** que estou autenticado no painel da operadora
- **Quando** acesso o dashboard de linhas
- **Então** vejo o total de embarques validados pelo AccessFlow hoje, por linha
- **E** consigo identificar a linha com maior volume de passageiros usando o cartão virtual

**Cenário: Filtro por modalidade de validação**
- **Dado** que estou analisando os dados de uma linha específica
- **Quando** aplico o filtro por modalidade de validação
- **Então** o gráfico deve dividir as validações entre NFC (HCE) e QR Code dinâmico

**Orientações para Implementação:** Pipeline de ingestão de eventos de validação em streaming; agregação por janela de tempo no banco analítico; registrar o evento de analytics `painel_operadora_linhas_consultado`.

**Id do passo:** 127

## US 25 — Fernanda — Visualizar relatórios regulatórios agregados de mobilidade

### Passo de Jornada
- **Fase do Roadmap Estratégico:** Fase 3 — Painel Web Institucional e Monetização
- **Jornada de Usuário:** Fernanda — Usar o painel institucional para fiscalizar a conformidade do AccessFlow em escala
- **Passo:** Acessar relatórios agregados de todas as operadoras conectadas

### Geral
- **Produto:** AccessFlow
- **Título:** Relatórios regulatórios e visão consolidada de mobilidade urbana
- **Narrativa:** Como Fernanda, representante do órgão público gestor de mobilidade, eu quero visualizar relatórios consolidados e anonimizados das validações no transporte da cidade, para fiscalizar a conformidade regulatória e subsidiar políticas públicas de mobilidade.
- **Prioridade:** Média | **Tipo:** Feature | **Coluna:** Backlog

### Detalhes
**Descrição Detalhada:** Módulo regulatório do painel web, voltado a órgãos reguladores e consórcios públicos, com a visão geral de uso do sistema, os índices de uso do benefício estudantil e do vale-transporte e a conformidade com os termos de permissão do serviço.

**Orientações de Tela:**
- Painel de visão regulatória consolidada;
- Gráficos de uso por tipo de tarifa (Estudante, VT e Avulso);
- Tabela com indicadores agregados por operadora parceira.

**Regras de Negócio:**
1. Todos os dados desta visão são anonimizados e agregados, em conformidade com a LGPD;
2. Os relatórios regulatórios não permitem identificar passageiros individualmente.

### BDD & Implementação

**Critérios de Aceitação (BDD)**

**Cenário: Relatório agregado sem identificadores pessoais**
- **Dado** que estou no painel regulatório do órgão público
- **Quando** gero o relatório consolidado mensal de viagens da cidade
- **Então** devo ver os totais de validações por categoria de benefício e por operadora
- **E** nenhum dado pessoal identificável (nome, CPF, foto) dos usuários deve aparecer no relatório

**Cenário: Exportação para auditoria regulatória**
- **Dado** que solicito a exportação dos dados de conformidade para o conselho
- **Quando** clico em "Exportar relatório regulatório (PDF)"
- **Então** deve ser gerado um documento formal, assinado digitalmente pelo sistema, com os índices operacionais exigidos em contrato

**Orientações para Implementação:** Aplicar técnicas de anonimização e agregação no banco analítico de relatórios; assinar os relatórios em PDF com certificado digital ICP-Brasil; registrar o evento de analytics `relatorio_regulatorio_gerado`.

**Id do passo:** 128
