# 📊 Estratégia de Negócio

## 👥 Segmentos de Clientes
O AccessFlow atende 2 segmentos primários, cada um representado por personas:

| Segmento | Personas Representativas | Quem são |
| :--- | :--- | :--- |
| **Usuários Finais do Transporte Público** | **Cynthia** (Estudante)<br>**Lucas** (Trabalhador CLT)<br>**Beatriz** (Usuária Avulsa) | Estudantes, trabalhadores assalariados e passageiros avulsos que utilizam o transporte público e hoje dependem de carteirinha estudantil, vale-transporte ou bilhete único. |
| **Administradores e Gestores** | **Ana** (Analista de RH)<br>**Roberto** (Gestor de Operadora)<br>**Fernanda** (Órgão Público) | Profissionais de RH de empresas, secretarias de instituições de ensino, operadoras de transporte e órgãos públicos de mobilidade responsáveis pela gestão, emissão e auditoria dos benefícios de transporte. |

> **Papel duplo de operadoras e órgãos públicos:** além de usarem o painel web (por isso Roberto e Fernanda são personas), operadoras de transporte e autoridades de mobilidade (como a ETUFOR) também são parceiras de integração com os validadores e de autorização dos pilotos.
> **Fora do foco inicial:** Gratuidades legais (como idosos e pessoas com deficiência), que dependem de regras específicas de cada município e entram como evolução futura.

---

## 🛠️ Jobs to be Done (JTBD)

### Usuários Finais do Transporte Público:

#### 1. Estudantes (Cynthia)
* **Funcional:**
- Usar o transporte público no dia a dia;
- Validar o embarque de forma rápida e confiável, mesmo sem internet;
- Manter controle do próprio saldo e histórico de uso;
- Recuperar o benefício rapidamente em caso de perda ou roubo, sem esperar pela segunda via.

#### 2. Trabalhadores (Lucas)
* **Funcional:**
- Utilizar o benefício de vale-transporte para o deslocamento casa-trabalho;
- Validar o embarque de forma garantida em horários de pico, mesmo offline;
- Acompanhar o crédito mensal disponibilizado pela empresa e ser avisado quando ele cair;
- Bloquear remotamente a credencial em caso de perda do aparelho;
- Manter controle do histórico de uso e do saldo restante.

#### 3. Usuários Avulsos (Beatriz)
* **Funcional:**
- Usar o transporte público de forma esporádica, sem depender de vínculo institucional;
- Validar o embarque por QR Code dinâmico, sem depender de NFC no celular;
- Realizar recargas avulsas e instantâneas via Pix, cartão de débito ou crédito;
- Manter controle em tempo real do próprio saldo e do histórico de viagens;
- Garantir o bloqueio remoto do saldo e a recuperação da credencial em caso de perda do aparelho.

---

### Administradores e Gestores:

#### 1. Analistas e Gestores de RH (Ana)
* **Funcional:**
- Cadastrar e importar colaboradores elegíveis e bloquear a credencial no desligamento;
- Configurar e automatizar a liberação do crédito mensal de vale-transporte por cargo ou grupo;
- Integrar a gestão de benefícios de transporte ao sistema de RH da empresa;
- Reduzir o volume de chamados de suporte sobre perda, saldo ou reemissão de cartões físicos;
- Fechar e exportar demonstrativos mensais de custos de transporte para o setor financeiro.

#### 2. Gestores de Operadoras de Transporte (Roberto)
* **Funcional:**
- Monitorar o volume e a taxa de sucesso das validações de embarque em tempo real, por linha de ônibus;
- Consultar e exportar o extrato das recargas atribuídas à frota, com o repasse e a comissão retida;
- Configurar regras de tarifa e validação alinhadas aos contratos de concessão;
- Garantir fluidez no embarque e reduzir os problemas na catraca enfrentados pelos motoristas.

#### 3. Representantes de Órgãos Públicos e Reguladores (Fernanda)
* **Funcional:**
- Auditar logs de segurança, padrões de uso e tentativas de fraude no sistema de bilhetagem;
- Acessar relatórios agregados de validação e recarga de todas as operadoras ativas na cidade;
- Fiscalizar a conformidade regulatória e a segurança da criptografia dos tokens de embarque;
- Emitir pareceres e relatórios técnicos embasados em dados consolidados de mobilidade.

---

## 🔎 Pesquisa com Usuários
Para validar o protótipo, a equipe aplicou uma pesquisa com 27 usuários de transporte público:
* **85%** aceitariam validar o embarque por QR Code dinâmico;
* **81%** consideram o modo offline essencial.

A amostra é pequena e serve como indício inicial do fit de solução. A validação deve ser ampliada no piloto com instituições e operadoras.

---

## 🧩 Problem-Solution Fit Canvas

### Causas Raízes e Soluções
| Segmento | Job | Dor (Problema) | Causa Raiz |
| :--- | :--- | :--- | :--- |
| **Usuários Finais** *(Estudantes)* | Embarcar só com o celular | Cartão esquecido ou perdido deixa o aluno retido na catraca. | O acesso depende exclusivamente de um objeto físico em PVC ou papel. |
| **Usuários Finais** *(Estudantes)* | Recarga remota | Filas e deslocamento até pontos presenciais tiram tempo. | O sistema legado exige a gravação física do saldo no chip do cartão. |
| **Usuários Finais** *(Trabalhadores)* | Passar sem travar a fila | Tarja ou chip gasto falha na leitura, ou o app trava sem sinal. | Leitores exigem contato físico e a validação depende de internet. |
| **Usuários Finais** *(Trabalhadores)* | Saber o crédito | Falta de transparência sobre o depósito do vale-transporte. | O sistema de crédito da empresa não se comunica com o app do usuário em tempo real. |
| **Usuários Finais** *(Avulsos)* | Embarque sem burocracia | Exclusão de quem não tem cartão de benefício ou tem celular sem NFC. | Ausência de leitura por QR Code e de alternativa digital imediata. |
| **Administradores e Gestores** | Bloqueio e emissão | Usuários desligados ou evadidos mantêm benefícios ativos; alto volume de chamados de 2ª via. | Cadastro engessado nas operadoras, sem controle direto e instantâneo do RH ou da secretaria. |
| **Administradores e Gestores** | Auditar e conciliar | Conciliação e prestação de contas dependem de planilhas manuais. | Dados fragmentados e separados dos sistemas de RH, acadêmicos e dos órgãos reguladores. |

**Solução:** Uma plataforma mobile-first que oferece o celular como alternativa ao cartão de transporte, com validação por NFC e QR Code dinâmico offline, recarga digital (Pix e cartão) e bloqueio remoto, integrada a um painel web B2B para gestão institucional e auditoria em tempo real.

**Gatilhos para agir:**
* *Usuários Finais (Estudantes):* Ficar retido na catraca; espera de até 2 meses pela 2ª via.
* *Usuários Finais (Trabalhadores):* Atraso por falha de leitura do cartão físico no ônibus; cartão roubado ou furtado.
* *Usuários Finais (Avulsos):* Necessidade de embarque imediato sem possuir cartão físico ou aparelho com NFC.
* *Administradores e Gestores:* Aumento no volume de chamados por perda de cartão, suspeitas de fraude na concessão e pressão da diretoria por redução de custos operacionais.

---

## 🏢 Business Model Canvas

* **Segmentos de Clientes:** Usuários Finais do Transporte Público (Estudantes, Trabalhadores CLT e Usuários Avulsos) e Administradores e Gestores (RHs de Empresas, Secretarias de Ensino, Operadoras de Transporte e Órgãos Públicos).
* **Propostas de Valor:**
  * *Usuários Finais:* O celular como alternativa de credencial de transporte (NFC e QR Code dinâmico offline), recarga instantânea por Pix ou cartão, alertas de saldo e de crédito na hora, extrato de viagens e proteção com bloqueio remoto.
  * *Administradores e Gestores:* Painel único de cadastro e importação em lote, automação de regras de benefício, bloqueio em tempo real, relatórios de auditoria e integração com sistemas de RH e acadêmicos. Reduz custos operacionais de reemissão e combate fraudes.
* **Canais:** App Mobile (Android e iOS), Painel Web Institucional, Parcerias Institucionais (Secretarias e RHs), Notificações Push, Divulgação no campus e na empresa, Vendas B2B Diretas.
* **Relacionamento:** Autoatendimento mobile, onboarding guiado B2B, notificações proativas de saldo, suporte por chat e transparência de status.
* **Fontes de Renda:** Licenciamento B2B (assinatura por usuário ativo ou por módulo), Comissão sobre Recargas (take-rate retido no repasse às operadoras), Taxa de Integração via API e Módulos Avançados de Auditoria e Relatórios.
* **Recursos-chave:** Plataforma App e Web, Validação NFC (HCE) e QR Dinâmico, Gateway de Pagamento, Infraestrutura em Nuvem, Criptografia e Conformidade com a LGPD.
* **Atividades-chave:** Desenvolvimento do software, Segurança dos tokens, Integrações (RH, Acadêmico e Operadoras), Suporte e Onboarding B2B e B2C, Monitoramento Antifraude.
* **Parcerias-chave:** Operadoras de Transporte, Órgãos Gestores de Mobilidade (ex.: ETUFOR), Gateways de Pix e Cartão, ERPs de Folha de Pagamento e Sistemas Acadêmicos.
* **Estrutura de Custo:** Nuvem e Hospedagem, Time de Desenvolvimento, Taxas Antifraude e de Pagamentos, Suporte B2B e B2C, Compliance Legal e Segurança da Informação.

> **Fluxo da receita de recargas:** o valor recarregado pelo usuário pertence ao sistema de transporte. O AccessFlow repassa esse valor às operadoras proporcionalmente às validações registradas em cada frota no período e retém a comissão acordada em contrato. O extrato da operadora (US 6) mostra recargas atribuídas, comissão retida e valor líquido repassado.

---

## 🎁 Canvas de Proposta de Valor (Resumo de Ganhos)

* **Produtos e Serviços:** App mobile-first para passageiros (NFC e QR Code dinâmico offline) e Painel Web B2B/B2G para gestão, automação de benefícios e auditoria em tempo real.
* **Aliviadores de Dores:** O celular elimina a dependência do cartão físico em PVC; recarga instantânea sem filas; QR Code como acesso universal para celulares sem NFC e como alternativa ao NFC; bloqueio imediato e sem taxas em caso de perda ou roubo; desligamento de funcionários e desvinculação de alunos direto pelo painel.
* **Criadores de Ganhos:** Confirmação de embarque em tempo real (feedback visual e tátil); alertas proativos de saldo baixo e de depósitos; compra e recarga avulsa instantânea via Pix; trilha de auditoria antifraude e relatórios financeiros automáticos; validação offline na catraca, sem depender de sinal.

---

## 🗺️ Roadmap Estratégico

**Pilares Estratégicos:**
1. Confiabilidade de validação em tempo real (NFC, QR Code e modo offline) para reduzir falhas e desencontros na catraca
2. Alternativa ao cartão físico por meio de uma credencial digital inclusiva, que funcione com ou sem NFC no aparelho do usuário
3. Conversão de ponta a ponta (cadastro → recarga → validação) com foco em reduzir filas e atrito no embarque
4. Regras e dados padronizados para instituições (empresas, escolas, operadoras), com gestão simples e rápida
5. Retenção e confiança via alertas automáticos de saldo, extrato completo e bloqueio remoto em caso de roubo
6. Go-to-market por parcerias institucionais e validação com usuários reais
7. Monetização progressiva (licenciamento institucional + comissão sobre recargas + contratos com operadoras) sem prejudicar a adoção
8. Ciclo contínuo de validação e aprendizado (pesquisa com usuários, testes de campo e loops de feedback com instituições)

> **Sobre durações e métricas:** cada fase corresponde a um grupo de iterações do Project Model Canvas, e a duração das iterações segue o calendário da disciplina. As métricas são metas para um piloto com usuários e instituições reais; no escopo da disciplina, elas orientam os testes com o protótipo e o validador simulado.

### Fase: 1
**Título:** MVP da Credencial Digital (Cartão Virtual e Validação)
**Duração:** 3 iterações (iterações 1 a 3)
**Foco estratégico:** Criar o núcleo de valor: um cartão virtual com validação por NFC ou QR Code dinâmico que funcione mesmo sem internet, oferecendo uma alternativa ao cartão físico no momento mais crítico, o embarque.
**Principais Entregas**
- Cadastro de usuário com perfil (foto, matrícula/ID, tipo de benefício) e consentimento LGPD
- Cartão virtual com QR Code dinâmico e suporte a NFC (HCE)
- Modo offline automático de validação, com credencial criptografada válida por até 48 horas
- Tela de validação no embarque com resultado em tempo real
- Validador simulado para testes, já que não há acesso a catracas reais

**Métricas**
- Tempo médio de validação na catraca: <= 5 segundos (máximo de 10 segundos até o resultado)
- Percentual de validações concluídas com sucesso na primeira tentativa: >= 90%
- Percentual de usuários piloto com cadastro completo e cartão virtual ativo: >= 70%
- Taxa de aceitação do modo offline como alternativa confiável, validada por pesquisa: >= 80%
- NPS (usuário final) na experiência de validação: >= 35

### Fase: 2
**Título:** Recarga, Saldo e Segurança (Confiança e Retenção)
**Duração:** 2 iterações (iterações 4 e 5)
**Foco estratégico:** Eliminar a dependência de pontos físicos de recarga e devolver ao usuário o controle sobre saldo e segurança, aumentando a confiança e o uso recorrente do app.
**Principais Entregas**
- Recarga via Pix, débito ou crédito diretamente pelo app
- Extrato e histórico de viagens e recargas em tempo real
- Alerta automático de saldo baixo
- Bloqueio remoto imediato e reemissão digital em caso de perda ou roubo

**Métricas**
- Usuários ativos semanalmente (recarregaram ou validaram ao menos 1x por semana): >= 50%
- Tempo médio para concluir uma recarga: <= 4 minutos
- Retenção D30 (usuários finais): >= 40%

### Fase: 3
**Título:** Painel Web Institucional e Monetização (Gestão e Escala)
**Duração:** 3 iterações (iterações 6 a 8)
**Foco estratégico:** Entregar a camada de gestão para instituições (empresas, escolas e operadoras de transporte), habilitando a escala de adoção e iniciando as primeiras fontes de receita.
**Principais Entregas**
- Painel web com cadastro, importação em lote e bloqueio de usuários
- Crédito mensal automático de vale-transporte, com aviso ao colaborador
- Extrato de recargas e comissão por operadora e fechamento financeiro mensal
- Dashboard com média diária de uso, recargas e saldo médio, por linha
- Relatórios regulatórios e logs de auditoria filtráveis por período, perfil e método de validação

**Métricas**
- Instituições piloto ativas no painel web: >= 2
- Percentual de elegíveis cadastrados em uma instituição piloto: >= 60%
- Tempo médio para um gestor ativar ou bloquear uma credencial pelo painel: <= 15 minutos
- Receita validada por comissão sobre recargas no piloto: primeira transação registrada
