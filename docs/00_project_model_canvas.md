# AccessFlow 🚌 📱

Credencial digital de transporte público por NFC e QR Code dinâmico

**Equipe:** Ismael, Salua, Suyana e Wesley  
**Disciplina:** Projeto Aplicado de Desenvolvimento de Software — UNIFOR  

---

## 🧠 1. POR QUÊ? (Justificativa e Objetivos)

### Justificativa
Usuários de transporte público dependem de cartões físicos (carteirinha estudantil, vale-transporte, bilhete avulso) que precisam ser recarregados em pontos físicos e que, em caso de perda ou roubo, podem levar até 2 meses para ter uma segunda via. Nesse período, o usuário paga a tarifa inteira do próprio bolso e perde o benefício. Além disso, empresas e escolas repetem todo mês processos manuais de crédito de benefício, e as operadoras e o órgão gestor têm pouca visibilidade sobre o uso real do sistema.

### Objetivo SMART
Até o fim do semestre letivo, entregar o MVP do AccessFlow (Fase 1) em 3 iterações, com as 11 histórias de usuário planejadas, permitindo que o usuário valide o embarque por NFC ou QR Code dinâmico com resultado em até 10 segundos, inclusive sem internet por até 48 horas, e completar o produto (recarga, segurança e painel institucional) em 8 iterações no total.

### Benefícios
* Celular como credencial: o usuário não depende mais do cartão físico para embarcar.
* Reemissão digital em minutos, eliminando a espera de até 2 meses pela segunda via.
* Validação offline, para uso em ônibus lotado ou sem sinal.
* Recarga e extrato pelo próprio app, sem ir a ponto físico.
* Automação do crédito mensal de vale-transporte para o RH das empresas.
* Dados de uso por linha para operadoras e relatórios anonimizados para o órgão regulador, em conformidade com a LGPD.
* Protótipo validado em pesquisa com 27 usuários de transporte público: 85% aceitariam QR Code dinâmico e 81% consideram o modo offline essencial.

---

## 📦 2. O QUÊ? (Produto e Requisitos)

### Produto
Plataforma com aplicativo mobile e portal web que oferece o celular como alternativa ao cartão de transporte: cartão virtual com foto, validação na catraca por NFC (HCE) ou QR Code dinâmico, recarga, extrato, bloqueio remoto e um painel web para instituições de ensino, empresas, operadoras de transporte e órgão gestor de mobilidade.

### Requisitos
* **Cadastro:** Cadastro de estudante (com matrícula), de trabalhador com vale-transporte corporativo e de usuário avulso.
* **Privacidade:** Termos de uso e consentimento de dados (LGPD), com consentimento específico para a foto, coletados antes da conclusão do cadastro.
* **Cartão virtual:** Cartão virtual com foto, nome, tipo de benefício, status e saldo.
* **Validação:** Validação por NFC (HCE) e por QR Code dinâmico (válido por até 30 segundos), com alternância automática entre os dois.
* **Modo offline:** Credencial criptografada no aparelho, válida por até 48 horas sem conexão, com limite de viagens calculado pelo saldo e sincronização automática ao voltar a conexão.
* **Feedback:** Resultado do embarque em até 10 segundos e histórico de viagens.
* **Recarga e saldo:** Recarga por Pix e por cartão de crédito/débito, alerta de saldo baixo e extrato com filtros.
* **Segurança:** Bloqueio remoto via web e reemissão digital da credencial no novo aparelho.
* **Painel web institucional:** Cadastro, importação e bloqueio de usuários, crédito automático de vale-transporte, extrato de comissão, auditoria de logs e fraude, fechamento financeiro mensal, dashboard por linha e relatórios regulatórios anonimizados.

---

## 👥 3. QUEM? (Stakeholders e Equipe)

### Stakeholders Externos
* **Usuários finais:** Estudantes, trabalhadores com vale-transporte e usuários avulsos do transporte público.
* **Escolas e universidades:** Instituições de ensino, que fornecem a lista de estudantes elegíveis.
* **Empresas parceiras:** Empresas e seus setores de RH, que enviam os dados dos funcionários e configuram o crédito mensal.
* **Operadoras:** Operadoras de transporte, que acompanham validações, repasses e comissão por linha.
* **Órgão regulador:** Órgão gestor ou consórcio de mobilidade urbana, responsável por fiscalizar a conformidade.
* **Parceiros tecnológicos:** Provedor de pagamentos (Pix e cartão) e fabricantes dos leitores das catracas.
* **Avaliação:** Professor da disciplina, que avalia o projeto.

### Equipe
* Ismael
* Salua
* Suyana
* Wesley

*Atividades compartilhadas entre os integrantes: levantamento de requisitos e histórias de usuário, UX/UI, desenvolvimento mobile e web, testes e documentação.*

---

## 🛠️ 4. COMO? (Premissas, Escopo e Restrições)

### Premissas
* A equipe trabalha com metodologia ágil: histórias de usuário com critérios BDD, priorização por valor e risco e entregas por iteração.
* Escolas e empresas fornecem a lista de elegíveis para validar os cadastros. Na Fase 1, essa lista é carregada pela equipe a partir de planilhas (CSV) enviadas pelas instituições piloto; a importação pelo próprio painel chega na Fase 3 (US 4).
* As operadoras disponibilizam leitores de catraca compatíveis com o token NFC/QR gerado pelo app. Durante o desenvolvimento, esse papel é cumprido por um validador simulado.
* Há um provedor de pagamentos com API de Pix e cartão disponível.
* O usuário pode ficar sem internet durante a viagem, por isso o app precisa funcionar offline.

### Grupos de Entregas
* **Fase 1 — MVP da Credencial Digital:** Cadastros (estudante, corporativo e avulso), consentimento LGPD, cartão virtual com foto, validação por NFC e QR Code, modo offline, resultado do embarque, histórico e validador simulado. *(Release 1, iterações 1 a 3)*.
* **Fase 2 — Recarga, Saldo e Segurança:** Recarga por Pix e cartão, alerta de saldo baixo, extrato, bloqueio remoto e reemissão digital. *(Release 2, iterações 4 e 5)*.
* **Fase 3 — Painel Web Institucional e Monetização:** Gestão de usuários no painel, crédito mensal automático, extrato de comissão, auditoria de fraude, fechamento financeiro, dashboard por linha e relatórios regulatórios. *(Release 3, iterações 6 a 8)*.

### Restrições
* Prazo limitado ao semestre letivo e equipe de quatro pessoas com dedicação parcial.
* Sem acesso a catracas e leitores reais, então a validação é simulada durante o desenvolvimento.
* Dependência de APIs de terceiros (pagamentos e verificação de elegibilidade), que podem ter limitações ou custos.
* No iOS, o uso de NFC por apps de terceiros exige acordo comercial e pagamento de taxas à Apple; por isso, nesta versão o iPhone valida o embarque pelo QR Code dinâmico.
* A foto facial é dado pessoal, possivelmente sensível, e exige tratamento conforme a LGPD.

---

## 📅 5. QUANDO E QUANTO? (Riscos, Cronograma e Custos)

### Riscos
* Incompatibilidade ou instabilidade do NFC (HCE) em diferentes aparelhos.
* Fraude ou uso duplicado de tokens na validação offline, e uso além do saldo enquanto o aparelho está sem conexão.
* Dificuldade de integração com os leitores das catracas das operadoras.
* Falhas na integração com o provedor de pagamentos e no recebimento do webhook do Pix.
* Não conformidade com a LGPD no tratamento da foto e dos dados pessoais.
* Baixa adesão de escolas, empresas e operadoras ao painel institucional.
* Atraso no cronograma por equipe com dedicação parcial.

### Linha do Tempo
* **Fase 1 — MVP (Iterações 1 a 3):** Cadastros de estudante e corporativo, consentimento LGPD, cartão virtual e resultado do embarque (iteração 1); validações NFC e offline (iteração 2); QR Code, alternância automática, cadastro avulso e histórico (iteração 3).
* **Fase 2 — Recarga e Segurança (Iterações 4 e 5):** Recarga por Pix e bloqueio remoto (iteração 4); reemissão, saldo baixo, extrato e recarga por cartão (iteração 5).
* **Fase 3 — Painel Web (Iterações 6 a 8):** Gestão de usuários no painel, crédito automático e comissão (iteração 6); auditoria e fechamento financeiro (iteração 7); dashboard por linha e relatórios regulatórios (iteração 8).
*(A duração de cada iteração segue o calendário da disciplina.)*

### Custos
* **Desenvolvimento:** Horas de trabalho da equipe, sem custo financeiro direto no contexto acadêmico.
* **Infraestrutura:** Hospedagem do backend e do painel web, banco de dados e armazenamento seguro das fotos.
* **Pagamentos:** Taxas por transação do provedor de pagamentos (Pix e cartão) quando o sistema operar em produção.
* **Publicação e certificação:** Conta de desenvolvedor nas lojas de aplicativos, caso o app seja publicado, e certificado digital ICP-Brasil para assinar os relatórios regulatórios.
* **Manutenção e suporte:** Correções e evolução após o lançamento, além de suporte aos usuários.
