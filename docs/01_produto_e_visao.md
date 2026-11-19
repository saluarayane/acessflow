# 📱 Produto e Visão: AccessFlow

**Documentação do Projeto** | Equipe: Ismael, Salua, Suyana e Wesley

## 📖 Descrição do Produto
O AccessFlow transforma o celular no cartão de transporte. O produto é composto por um aplicativo mobile e um painel web que oferecem uma alternativa digital aos cartões físicos de PVC e papel, como a carteirinha estudantil, o vale-transporte e o bilhete único.

No aplicativo, o usuário tem um cartão virtual com foto, nome, matrícula ou ID e tipo de benefício. O embarque é validado por aproximação (NFC) ou por QR Code dinâmico, inclusive sem internet, graças ao modo offline automático. O usuário também pode recarregar créditos por Pix, cartão de crédito ou débito, consultar saldo, extrato e histórico de viagens, receber alertas e bloquear o cartão remotamente em caso de perda ou roubo do celular.

No painel web, empresas, instituições de ensino e operadoras podem cadastrar usuários, definir regras de benefício e recarga, acompanhar o uso em tempo real, emitir relatórios de auditoria e integrar o sistema à folha de pagamento, aos sistemas acadêmicos e à bilhetagem eletrônica.

> **Nota Técnica:** A validação por NFC usa HCE (Host Card Emulation) no Android. No iPhone, o embarque é feito pelo QR Code dinâmico: desde o iOS 18.1 a Apple libera o NFC para apps de terceiros no Brasil, mas exige acordo comercial e cobrança de taxas, o que fica fora do escopo desta versão.

---

## 🎯 Matriz: É, Não é, Faz e Não faz

### ✅ O que É
* Plataforma de benefícios e pagamentos de transporte público, com app mobile e painel web.
* Alternativa digital aos cartões físicos (carteirinha estudantil, vale-transporte e bilhete único).
* Credencial de embarque no celular, por NFC (HCE) ou QR Code dinâmico.
* Camada de software e API que se integra aos validadores e catracas existentes.
* Experiência mobile-first que funciona mesmo sem internet (modo offline automático).

### ❌ O que Não É
* Carteira digital de pagamentos gerais (como PicPay, Mercado Pago ou Apple Pay).
* Sistema de gestão de frotas, rotas ou motoristas.
* Aplicativo de planejamento de rotas ou navegação (como Google Maps ou Moovit).
* Hardware de catraca, validador ou leitor.
* Sistema acadêmico ou de RH completo (não lança notas, faltas ou folha de pagamento).
* Controle de acesso a prédios ou áreas privadas de instituições.
* Ferramenta para clonar ou duplicar cartões físicos de terceiros.

### ⚙️ O que Faz
* Emite o cartão virtual com foto, nome, matrícula ou ID.
* Valida o embarque por aproximação (NFC) ou por QR Code dinâmico, inclusive offline.
* Gera tokens assinados, temporários e de curta duração, no próprio aparelho.
* Permite recarga por Pix, cartão de crédito ou débito.
* Mostra saldo, extrato e histórico de viagens, e envia notificações de saldo baixo, de crédito recebido e de status.
* Permite bloqueio remoto imediato e reemissão digital do cartão.
* Permite à empresa ou instituição cadastrar e importar usuários, definir regras de benefício e bloquear ou desbloquear credenciais pelo painel web.

### 🚫 O que Não Faz
* Não realiza pagamentos em comércio, serviços ou aplicativos de delivery.
* Não concede descontos em cinema, eventos ou outros serviços.
* Não substitui sistemas de gestão acadêmica nem de RH.
* Não gerencia frota, rotas ou motoristas das operadoras.
* Não substitui catracas, validadores ou sensores dos terminais e veículos.
* Não define tarifas nem gratuidades legais: aplica as regras definidas por operadoras e instituições.
* Não substitui o atendimento presencial em casos que exijam documentação oficial.
* Não valida embarque por NFC no iPhone nesta versão: nele, a validação é por QR Code dinâmico.

---

## 🔭 Visão do Produto

* **Para:** Usuários do transporte público (estudantes, trabalhadores CLT e passageiros avulsos) e os administradores e gestores que concedem, gerenciam e fiscalizam esses benefícios (RHs, secretarias acadêmicas, operadoras de transporte e órgãos reguladores).
* **Que dores enfrentam:** Filas na catraca; cartões físicos esquecidos, danificados ou roubados; espera de até 2 meses pela segunda via; incerteza sobre saldo e depósitos no momento do embarque; e gestão manual, burocrática e pouco transparente para as instituições.
* **O:** AccessFlow.
* **Que benefícios:** Transforma o celular na credencial de embarque, com validação por NFC ou QR Code dinâmico (inclusive offline), recargas digitais via Pix ou cartão, acompanhamento de saldo em tempo real e bloqueio remoto imediato.
* **É uma:** Plataforma mobile-first de bilhetagem e mobilidade urbana, composta por um aplicativo para o usuário final e um painel web B2B/B2G de gestão, automação e auditoria em tempo real.
* **Diferente de:** Soluções tradicionais de recarga, como o Libercard, que ainda dependem da gravação física e do uso do cartão de PVC na catraca, e de aplicativos de bilhetagem por QR Code já adotados em outras cidades, como o Jaé, no Rio de Janeiro.
* **O nosso produto:** Reúne em uma só plataforma a credencial no celular com NFC e QR Code, a validação que continua funcionando sem sinal de internet e um painel para empresas, escolas, operadoras e órgãos reguladores gerirem benefícios, crédito automático e auditoria.
