# Curso de Adquirência

### Do Zero ao Profissional — Onboarding Executivo/Técnico para um Head de AI em uma Adquirente

> **Para quem é este curso:** você é engenheiro/gestor de tecnologia, forte em produto, arquitetura e automação com IA, e vai assumir como **Head de AI** numa adquirente. Você não precisa virar especialista regulatório — precisa entender o **negócio**, os **fluxos**, **onde está o dinheiro e o risco**, e **onde IA gera valor real sem criar problema**.
>
> **Como ler:** cada módulo tem conceitos → fluxo com setas → exemplo numérico → caso prático → perguntas de revisão. No fim há um **cheat sheet de 2 páginas** para memorizar.
>
> **Aviso sobre regulação:** trato regulação de forma conceitual e correta, mas **não invento número de norma**. Onde aparecer ⚠️ **[VALIDAR COM JURÍDICO/COMPLIANCE]**, trate como ponto a confirmar internamente — regras do Banco Central mudam e o detalhe importa.

---

## SUMÁRIO

1. [Sumário executivo](#sumario-executivo)
2. [Mapa mental do mercado](#mapa-mental)
3. [Glossário essencial](#glossario)
4. Módulo 1 — Fundamentos de adquirência
5. Módulo 2 — Fluxo de uma transação de cartão
6. Módulo 3 — Economia da transação
7. Módulo 4 — EC, onboarding, KYC e KYB
8. Módulo 5 — POS, maquininha e conectividade
9. Módulo 6 — Subadquirência, subcontas e marketplaces
10. Módulo 7 — Split de pagamento
11. Módulo 8 — Liquidação, recebíveis e antecipação
12. Módulo 9 — Backoffice de adquirência
13. Módulo 10 — Chargeback, fraude e risco
14. Módulo 11 — Conciliação
15. Módulo 12 — E-commerce, gateway e APIs
16. Módulo 13 — Pix e outros meios
17. Módulo 14 — Regulação no Brasil
18. Módulo 15 — Players do mercado
19. Módulo 16 — Tecnologia de uma adquirente
20. Módulo 17 — AI aplicada à adquirência
21. Módulo 18 — Roadmap V1/V2/V3
22. Módulo 19 — Plano 30/60/90 dias
23. Módulo 20 — Perguntas internas por área
24. [Casos reais simulados](#casos)
25. [Checklist para começar bem no cargo](#checklist)
26. [Fontes e regulação para estudar depois](#fontes)
27. [CHEAT SHEET (2 páginas)](#cheatsheet)

---

<a name="sumario-executivo"></a>
## 1. Sumário Executivo

Uma **adquirente** (ou **credenciadora**) é a empresa que permite que um lojista **aceite cartão** e garante que o dinheiro da venda chegue até ele. Ela fica **no meio** entre o lojista e o sistema de cartões (bandeiras + bancos emissores), e cobra por isso.

O negócio se sustenta em três fontes de receita, nesta ordem de importância na maioria das casas brasileiras:

1. **MDR** (a taxa cobrada do lojista por transação) — receita recorrente e visível.
2. **Antecipação de recebíveis** (adiantar ao lojista um dinheiro que ele só receberia em 30 dias, cobrando um desconto) — frequentemente **a maior margem** do negócio no Brasil.
3. **Hardware e serviços** (aluguel/venda de maquininha, gateway, split, antifraude).

A adquirente **assume riscos** em troca dessa receita: principalmente **chargeback** (a venda é desfeita depois que o lojista já recebeu) e **risco de crédito da antecipação** (você adiantou dinheiro de uma venda que pode ser cancelada/contestada).

Para você, como Head de AI, a tradução é direta: **o negócio é uma máquina de eventos financeiros de altíssimo volume**, cheia de **trabalho manual** (onboarding, análise de risco, contestação de chargeback, conciliação, suporte), com **dados ricos e estruturados** e **decisões hoje tomadas no feeling**. Isso é terreno fértil para IA — desde que você respeite **dados sensíveis (PCI/LGPD)**, **explicabilidade** e **auditoria**, porque aqui IA toca dinheiro de terceiros e decisão regulada.

**A frase para guardar:** *a adquirente ganha dinheiro intermediando risco e prazo; a IA ganha valor reduzindo trabalho manual, perda financeira e churn sem aumentar risco regulatório.*

---

<a name="mapa-mental"></a>
## 2. Mapa Mental do Mercado

```
                                  ┌─────────────────────────────┐
                                  │        BANCO CENTRAL        │  (regula arranjos e IPs)
                                  └──────────────┬──────────────┘
                                                 │ regula
                 ┌───────────────────────────────┼───────────────────────────────┐
                 │                                │                                │
        ┌────────▼────────┐              ┌────────▼────────┐              ┌────────▼────────┐
        │   BANDEIRAS      │              │   REGISTRADORAS  │             │  ARRANJOS/PIX    │
        │ Visa/Master/Elo  │              │ CERC/TAG/Núclea  │             │ (regras do meio) │
        └────────┬─────────┘              └────────┬─────────┘             └──────────────────┘
                 │ regras + intercâmbio            │ registra recebíveis
   ┌─────────────┴───────────────┐                 │
   │ POLO PAGADOR                │                 │            POLO RECEBEDOR
   │ ┌─────────┐   ┌──────────┐  │                 │     ┌──────────┐   ┌──────────────┐
   │ │ PORTADOR │─▶│ EMISSOR  │  │                 └────▶│ ADQUIRENTE│──▶│      EC      │
   │ │(cliente) │  │(Nubank,  │  │   autorização         │(Cielo,    │   │(lojista/loja)│
   │ └─────────┘   │ Itaú...) │◀─┼───────────────────────│ Stone...) │   └──────┬───────┘
   │               └──────────┘  │   liquidação           └────┬─────┘          │
   └─────────────────────────────┘                            │ usa            │ pode estar sob
                                                    ┌──────────▼───────┐        ▼
                                                    │  PROCESSADORA    │   ┌─────────────────┐
                                                    │ (switch técnico) │   │ SUBADQUIRENTE   │
                                                    └──────────────────┘   │ (PagBank, MP,   │
                                                                           │  marketplaces)  │
                                                                           └────────┬────────┘
                                                                                    │ agrega
                                                                              ┌─────▼──────┐
                                                                              │  SUB-ECs    │
                                                                              │ (sellers)   │
                                                                              └────────────┘
                       GATEWAY = "trilho técnico" que conecta EC ↔ adquirente(s), sem assumir risco
```

**Leitura do mapa em uma frase:** o portador paga com o cartão do **emissor**; a **bandeira** carrega a transação sob suas regras; a **adquirente** captura, liquida e paga o **EC**; **subadquirentes** revendem isso para muitos sellers; **gateways** são o encanamento técnico; **registradoras** e **Banco Central** organizam recebíveis e regras.

---

<a name="glossario"></a>
## 3. Glossário Essencial

| Sigla / Termo | Significado | Em uma frase |
|---|---|---|
| **EC** | Estabelecimento Comercial | O lojista que aceita cartão. |
| **Portador** | Cardholder | Quem usa o cartão para pagar. |
| **Emissor** | Issuer | Banco/IP que emitiu o cartão (Nubank, Itaú). |
| **Bandeira** | Scheme/Network | Visa, Mastercard, Elo, Amex, Hipercard. Dona das regras. |
| **Adquirente / Credenciador** | Acquirer | Habilita o EC, captura, liquida e paga. |
| **Subadquirente / Subcredenciador** | Sub-acquirer / Payment Facilitator | Opera "em cima" de uma adquirente e agrega muitos sub-ECs. |
| **Gateway** | Payment gateway | Roteia transações tecnicamente; não liquida nem assume risco. |
| **PSP** | Payment Service Provider | Guarda-chuva: pode ser gateway + subadquirente + serviços. |
| **Processadora** | Processor | Faz o processamento técnico (switch/autorização). |
| **IP** | Instituição de Pagamento | Figura regulatória do BACEN (credenciadora, emissora etc.). |
| **MDR** | Merchant Discount Rate | Taxa cobrada do EC por transação. |
| **Interchange** | Tarifa de intercâmbio | Parte do MDR que vai ao **emissor**. |
| **Scheme fee** | Tarifa da bandeira | Parte que vai à **bandeira**. |
| **Net MDR / Take rate** | MDR líquido | O que sobra para a adquirente após interchange + scheme fee. |
| **TPV** | Total Payment Volume | Volume financeiro transacionado (métrica-chave do negócio). |
| **NSU** | Número Sequencial Único | Identificador da transação no terminal/adquirente. |
| **TID** | Terminal ID | Identificador lógico do terminal POS. |
| **MCC** | Merchant Category Code | Código de 4 dígitos da bandeira que classifica o ramo do EC. |
| **CNAE** | Classificação Nacional de Atividade Econômica | Classificação fiscal da atividade (Receita). |
| **KYC / KYB** | Know Your Customer / Business | Conhecer o cliente PF / o negócio PJ no onboarding. |
| **Underwriting** | Análise de risco de crédito/aceite | Decisão de aprovar, com qual limite e reserva. |
| **Chargeback** | Estorno por contestação | Portador contesta; a venda é desfeita e debitada do EC. |
| **Liquidação / Settlement** | Pagamento ao EC | Transferência efetiva do dinheiro da venda ao lojista. |
| **Recebível de arranjo** | UR (Unidade de Recebível) | Direito do EC de receber o valor de vendas futuras. |
| **Antecipação** | Adiantamento de recebíveis | Pagar o EC antes do prazo, cobrando desconto. |
| **Split** | Divisão de pagamento | Dividir o valor de uma venda entre vários recebedores. |
| **Conciliação** | Reconciliation | Bater registros entre adquirente, banco, ERP, gateway. |
| **Pix** | Arranjo instantâneo do BACEN | Pagamento 24/7 liquidado em segundos. |
| **TEF** | Transferência Eletrônica de Fundos | Integração do PDV/ERP com a captura via PIN pad. |
| **PAN** | Primary Account Number | O número do cartão (dado sensível, PCI). |
| **PCI DSS** | Payment Card Industry Data Security Standard | Norma de segurança de dados de cartão. |
| **CNP / CP** | Card Not Present / Card Present | Compra online (sem cartão físico) / presencial. |
| **3DS** | 3-D Secure | Autenticação do portador no online (transfere responsabilidade). |
| **MED** | Mecanismo Especial de Devolução (Pix) | Caminho de "disputa" do Pix em caso de fraude/golpe. |

---

# MÓDULO 1 — Fundamentos de Adquirência

## 1.1 O que é adquirência
**Adquirência** é o serviço de **habilitar um lojista a aceitar instrumentos de pagamento** (cartão de crédito/débito, e hoje Pix e carteiras) e **garantir o repasse** do valor das vendas. A empresa que faz isso é a **adquirente** (no vocabulário regulatório, **credenciadora**).

> **Analogia:** a adquirente é o "caixa eletrônico ao contrário" do lojista. O cliente coloca dinheiro no sistema (paga), e a adquirente é quem garante que esse dinheiro pingue na conta do lojista — depois de tirar sua taxa e checar que ninguém vai contestar.

## 1.2 O que é credenciamento
**Credenciamento** é o ato de **cadastrar e habilitar o EC** dentro de um ou mais arranjos de pagamento (ex.: habilitar a loja para aceitar Visa crédito, Mastercard débito etc.). Inclui análise cadastral, de risco, definição de taxas, MCC, limites e configuração de captura.

## 1.3 Quem é quem (diferenças que você não pode confundir)

| Papel | Faz o quê | Assume risco financeiro? | Liquida o EC? | Exemplo |
|---|---|---|---|---|
| **Adquirente / Credenciador** | Habilita o EC, captura, liquida, paga | **Sim** (chargeback, antecipação) | **Sim** | Cielo, Rede, Getnet, Stone |
| **Subadquirente / Subcredenciador** | Agrega muitos sub-ECs sob seu contrato com a adquirente | **Sim**, sobre seus sub-ECs (conforme contrato) | Repassa (liquida via adquirente) | PagBank, Mercado Pago, marketplaces |
| **Gateway** | Roteia a transação tecnicamente | **Não** | **Não** | Braspag, gateways "puros" |
| **PSP** | Guarda-chuva: gateway + sub + serviços | Depende do que ele é | Depende | pagar.me, EBANX (modelos variados) |
| **Processadora** | Switch/autorização técnica | **Não** (presta serviço técnico) | **Não** | Fiserv, Global Payments, processadoras locais |
| **Bandeira** | Dona das regras do arranjo, define interchange/scheme fee | **Não** (define risco, não financia) | **Não** | Visa, Mastercard, Elo |
| **Emissor** | Emite o cartão, banca o crédito do portador | **Sim** (risco de crédito do portador) | — | Nubank, Itaú |
| **Instituição domicílio** | Onde o EC recebe a liquidação | — | Recebe os recursos para o EC | Banco do lojista / conta de pagamento |

> **Teste rápido para classificar qualquer empresa:** pergunte **"quem assume o chargeback?"** e **"quem aparece para a bandeira/liquidação centralizada?"**. Se ela assume risco e liquida → adquirente/credenciador. Se agrega sellers em cima de uma adquirente → subadquirente. Se só roteia bytes → gateway.

## 1.4 Atores do ecossistema
**Portador → EC (lojista) → Adquirente → Bandeira → Emissor**, com **subadquirente** podendo se inserir entre EC e adquirente, **registradora** organizando recebíveis e **Banco Central** regulando tudo. A **instituição domicílio** é onde o dinheiro do EC aterrissa.

## 1.5 Polo pagador × polo recebedor
- **Polo pagador (polo emissor):** o lado de **quem paga** — portador + emissor. O emissor banca o limite e cobra o cliente na fatura.
- **Polo recebedor (polo credenciador):** o lado de **quem recebe** — EC + adquirente. A adquirente garante o repasse.

```
PORTADOR ──paga──▶ [POLO PAGADOR: EMISSOR] ──$ via arranjo──▶ [POLO RECEBEDOR: ADQUIRENTE] ──paga──▶ EC
```

## 1.6 Como a adquirente ganha dinheiro
1. **MDR** sobre cada venda (take rate).
2. **Antecipação** de recebíveis (spread/desconto) — muitas vezes a maior margem.
3. **Aluguel/venda de POS** e mensalidades.
4. **Serviços**: gateway, antifraude, split, conciliação, tokenização.
5. **Float** e tarifas avulsas.

## 1.7 Adquirente × subadquirente × gateway (resumo)

| Critério | Adquirente | Subadquirente | Gateway |
|---|---|---|---|
| Relação direta com bandeira | Sim | Não (via adquirente) | Não |
| Assume chargeback | Sim | Sim (dos sub-ECs) | Não |
| Liquida o lojista | Sim | Repassa | Não |
| Receita principal | MDR + antecipação | Markup sobre adquirente + serviços | Tarifa por transação/integração |
| Para quem vende | ECs diretos | Muitos sellers/plataformas | Quem precisa de integração técnica |

### Exemplo prático
Uma startup quer "aceitar cartão no app dela e pagar 50 vendedores". Ela **não** vira adquirente (custo regulatório/tecnológico altíssimo). Ela vira **subadquirente** sobre uma adquirente, usa **split** para pagar os 50 vendedores e talvez um **gateway** para o encanamento. A adquirente enxerga **uma conta** (a startup) com muitos sub-ECs por trás.

### Perguntas de revisão
1. Qual a diferença prática entre "credenciador" e "adquirente"?
2. Quem assume o chargeback num arranjo de subadquirência?
3. Por que antecipação costuma ser mais rentável que o MDR puro?

---

# MÓDULO 2 — Fluxo de uma Transação de Cartão

## 2.1 As duas grandes fases
Toda transação tem **duas fases que muita gente confunde**:
- **Autorização (online, em ~1–3s):** "esse cartão tem limite e é válido?" → reserva o valor.
- **Liquidação/clearing (em lote, depois):** "movimenta o dinheiro de verdade" entre emissor, bandeira, adquirente e EC.

> **Analogia:** autorização é o **"reservar a mesa"** no restaurante; liquidação é **"pagar a conta no fim"**. Reservar não move dinheiro; pagar move.

## 2.2 Fluxo presencial (cartão presente — CP)
```
Cliente passa o cartão no POS
   └─▶ CAPTURA (POS lê chip/aproximação)
        └─▶ AUTORIZAÇÃO: POS ─▶ Adquirente ─▶ Processadora ─▶ Bandeira ─▶ EMISSOR
                                                                          │ (checa limite, antifraude, senha)
        └◀── RESPOSTA: EMISSOR ─▶ Bandeira ─▶ Adquirente ─▶ POS  (Aprovada/Negada)
   └─▶ CONFIRMAÇÃO DA VENDA (comprovante, NSU)
... horas depois ...
   └─▶ CLEARING: bandeira consolida os lotes do dia
   └─▶ SETTLEMENT/LIQUIDAÇÃO: dinheiro caminha emissor → bandeira → adquirente
   └─▶ REPASSE AO LOJISTA: adquirente paga o EC em D+x (líquido de MDR)
```

## 2.3 Fluxo online (cartão não presente — CNP)
```
Checkout no site/app
   └─▶ CAPTURA: dados do cartão (ou TOKEN) ─▶ Gateway ─▶ Adquirente
        └─▶ (opcional) 3DS: autentica o portador no banco emissor
        └─▶ (opcional) ANTIFRAUDE: score antes de autorizar
        └─▶ AUTORIZAÇÃO ─▶ Bandeira ─▶ Emissor ─▶ RESPOSTA
   └─▶ CAPTURE/CONFIRMAÇÃO (pode ser separada da autorização: "auth" e depois "capture")
   └─▶ CLEARING ─▶ SETTLEMENT ─▶ REPASSE
```
**Diferença-chave:** no online não há chip/senha por padrão → **maior risco de fraude** → entram 3DS, antifraude e tokenização.

## 2.4 Estados de uma transação (não confundir!)
| Estado | O que significa |
|---|---|
| **Autorizada** | Emissor aprovou e reservou o limite. Dinheiro ainda não andou. |
| **Capturada** | Lojista confirmou a venda; entra para liquidação. |
| **Liquidada** | Dinheiro efetivamente repassado ao EC (D+x). |
| **Cancelada** | Desfeita **antes** da liquidação (ex.: estorno no mesmo dia). |
| **Estornada (refund)** | Devolução **após** captura/liquidação, iniciada pelo lojista. |
| **Contestada (chargeback)** | Devolução **forçada pelo portador** via emissor/bandeira. |

```
AUTORIZADA ─▶ CAPTURADA ─▶ LIQUIDADA
     │            │             │
     ▼            ▼             ▼
 (cancela)    (estorna)    (chargeback)
```

## 2.5 Exemplo completo — compra de R$100 no crédito à vista
```
1. Cliente paga R$100 no POS.
2. Autorização aprovada (limite ok).               → estado: AUTORIZADA
3. Lojista confirma a venda.                        → estado: CAPTURADA
4. À noite, bandeira faz o clearing do lote.
5. Liquidação: adquirente recebe e repassa ao EC.   → estado: LIQUIDADA
6. EC recebe R$97,50 (MDR de 2,5% = R$2,50) em D+30 — ou D+1 se antecipar.
```
Veja o destino de cada centavo no **Módulo 3**.

### Perguntas de revisão
1. Qual a diferença entre autorização e liquidação?
2. Por que "capturada" não é igual a "liquidada"?
3. No online, o que entra no lugar do chip/senha para conter fraude?

---

# MÓDULO 3 — Economia da Transação

## 3.1 Os componentes do MDR
**MDR = Interchange + Scheme fee + Margem da adquirente (Net MDR)**

| Componente | Quem fica com ele | Ordem de grandeza* |
|---|---|---|
| **Interchange** | Emissor | a maior fatia |
| **Scheme fee** | Bandeira | pequena |
| **Net MDR / take rate** | Adquirente | o que sobra |

\* *Valores reais variam por bandeira, produto, MCC e contrato — ⚠️ confirme as tabelas vigentes internamente; há **teto regulatório para intercâmbio no débito** no Brasil cujo número exato deve ser **[VALIDAR COM JURÍDICO/COMPLIANCE]**.*

## 3.2 Exemplo — venda de R$100 no crédito (MDR 2,5%)
```
Venda................................. R$100,00
MDR total (2,5%)...................... R$  2,50
   ├─ Interchange (~1,30%)............ R$  1,30  → vai ao EMISSOR
   ├─ Scheme fee (~0,13%)............. R$  0,13  → vai à BANDEIRA
   └─ Net MDR (~1,07%)................ R$  1,07  → fica com a ADQUIRENTE
EC recebe............................. R$ 97,50  (em D+30, se não antecipar)
```
A adquirente fica com **R$1,07 brutos** — dela ainda saem custos de processamento, POS, suporte, perdas de chargeback etc. **Margem fina por transação, ganha-se na escala (TPV).**

## 3.3 A segunda receita: antecipação
Se o EC quer o dinheiro **hoje** (D+1/D+0) em vez de D+30, a adquirente **antecipa** cobrando um **desconto** (ex.: ~1,5%–2,5% a.m., ⚠️ varia muito).
```
EC venderia R$100 a receber em D+30.
Antecipa por ~2% → recebe ~R$98 hoje (sobre o líquido).
A adquirente embolsa esse desconto como receita financeira.
```
Por isso, em muitas adquirentes brasileiras, **a antecipação rivaliza ou supera o MDR em margem**.

## 3.4 Débito × crédito à vista × crédito parcelado
| Produto | Risco | Prazo típico de liquidação | MDR típico | Particularidade |
|---|---|---|---|---|
| **Débito** | Baixo (saldo na conta) | D+1 | menor | Intercâmbio com teto regulatório ⚠️ |
| **Crédito à vista** | Médio (chargeback) | D+30 (padrão BR) | médio | Base da antecipação |
| **Crédito parcelado lojista** | Maior (prazo longo) | parcela a parcela (D+30, D+60…) | maior | "Parcelado sem juros" do varejo BR; ótimo para antecipação |

> **Particularidade brasileira:** o **parcelado lojista** (loja parcela "sem juros") cria recebíveis longos (até 12x) → mina de ouro para antecipação, mas também concentra risco de prazo.

## 3.5 Como precificar lojistas
A taxa de um EC sai de uma combinação de:
```
MDR do EC = custo (interchange + scheme + processamento + perda esperada de chargeback + custo de capital)
          + margem-alvo
          − desconto por volume/estratégia comercial
```
**3 alavancas que mexem na margem:**
- **Volume (TPV):** mais volume dilui custos fixos → pode dar taxa menor mantendo margem.
- **Risco:** MCC arriscado → mais chargeback → taxa/reserva maiores.
- **Prazo:** quanto mais longo o recebível, mais valor de antecipação (mas mais risco).

### Exemplo de decisão de produto
Um EC grande de eletrônicos (MCC de risco médio-alto, ticket alto, muito parcelado) pede taxa baixíssima. Decisão: **dar MDR competitivo** porque o **volume + parcelado** geram **antecipação** rentável — desde que o **risco de chargeback** seja contido com reserva e monitoramento.

### Perguntas de revisão
1. Para onde vão os R$2,50 de MDR numa venda de R$100?
2. Por que parcelado lojista é atraente para a adquirente?
3. Como volume, risco e prazo puxam a margem em direções diferentes?

---

# MÓDULO 4 — EC, Onboarding, KYC e KYB

## 4.1 O que é o EC e por que o onboarding importa
O **EC** é o cliente que paga MDR — mas também é **a porta de entrada de risco** (fraude, lavagem, chargeback). Onboarding ruim = prejuízo lá na frente.

## 4.2 Fluxo de cadastro de um lojista
```
Lead comercial ─▶ Coleta de dados/documentos ─▶ KYC/KYB (validação) ─▶ MCC/CNAE
   ─▶ UNDERWRITING (risco) ─▶ Decisão: APROVA / REJEITA / PENDÊNCIA
   ─▶ Define TAXAS + LIMITES + RESERVA ─▶ Habilita captura (POS/online) ─▶ Monitora
```

## 4.3 Dados necessários
| Pessoa Física (PF) | Pessoa Jurídica (PJ) |
|---|---|
| Nome, CPF, data de nascimento | Razão social, CNPJ |
| Documento com foto | Contrato/estatuto social |
| Comprovante de endereço | Quadro de sócios (QSA) e CPFs |
| Conta de domicílio (onde recebe) | Conta de domicílio da empresa |
| Selfie/biometria (antifraude) | Documentos dos sócios + KYC de cada |

## 4.4 KYC e KYB
- **KYC (Know Your Customer):** validar a **pessoa** (identidade, antifraude, listas restritivas/PEP, sanções).
- **KYB (Know Your Business):** validar a **empresa** (existência, CNAE, sócios reais, beneficiário final, situação cadastral).

> ⚠️ KYC/KYB conectam-se a **PLD/FT** (prevenção à lavagem) — escopo de **compliance**. Trate regras específicas como **[VALIDAR COM COMPLIANCE]**.

## 4.5 MCC × CNAE
- **MCC (Merchant Category Code):** código de 4 dígitos **da bandeira** → afeta **interchange, regras e risco**.
- **CNAE:** classificação **fiscal** da atividade (Receita).
- O backoffice precisa **mapear CNAE → MCC** e detectar incoerências (ex.: CNAE de "consultoria" mas o EC parece vender eletrônicos = sinal de risco).

## 4.6 Underwriting, decisão e limites
- **Underwriting:** análise de risco que define **aprovar, com que limite, com qual reserva**.
- **Limites operacionais:** teto de venda diária/mensal, ticket máximo.
- **Reserva de risco (rolling reserve):** reter % do volume por um período para cobrir chargeback futuro.
- Decisões: **aprovar / rejeitar / bloquear** (bloqueio pode vir depois, por comportamento suspeito).

### Exemplo prático
EC novo, MCC de "infoprodutos" (alto chargeback). Decisão: aprovar com **limite diário baixo**, **reserva de 10% por 90 dias** e **monitoramento reforçado**. Conforme o histórico melhora, **afrouxa** limites.

### Onde IA já ajuda aqui
- Classificação automática **CNAE→MCC** e detecção de incoerência.
- Leitura e validação de documentos (OCR + verificação).
- **Underwriting assistido**: score de risco + explicação (sempre com humano na decisão final em casos sensíveis).

### Perguntas de revisão
1. Diferença prática entre MCC e CNAE?
2. O que é reserva de risco e quando usá-la?
3. Por que onboarding ruim vira prejuízo de chargeback depois?

---

# MÓDULO 5 — POS, Maquininha e Conectividade

## 5.1 Conceitos
- **POS (Point of Sale):** o ponto de captura presencial — a "maquininha".
- **TEF (Transferência Eletrônica de Fundos):** integração do **PDV/ERP** do lojista com a captura, normalmente via **PIN pad** conectado ao caixa.
- **PIN pad:** periférico que lê cartão/senha, plugado a um computador/PDV.
- **Smart POS / POS Android:** terminal com Android que roda apps (vendas, fidelidade, gestão).
- **Gateway / checkout online:** captura no mundo web (sem hardware físico).

| Forma de captura | Onde vive | Quando usar |
|---|---|---|
| Maquininha (POS) | Balcão | PME, autônomo, varejo de rua |
| PIN pad + TEF | Caixa integrado ao ERP | Varejo com PDV/retaguarda |
| Smart POS Android | Balcão "inteligente" | Quem quer apps no terminal |
| Gateway/checkout | Site/app | E-commerce, assinaturas |

## 5.2 Chip mono × chip multi
- **Chip mono(-aplicação):** o cartão hospeda **um** aplicativo de pagamento.
- **Chip multi(-aplicação):** hospeda **vários** (ex.: crédito + débito + benefícios no mesmo chip) → o terminal precisa **selecionar o AID** (Application Identifier) certo e, às vezes, **perguntar ao portador** qual usar.
- Relacionado: terminais **mono-adquirente** (capturam só de uma adquirente) × **multi-adquirente** (capturam de várias). ⚠️ O uso exato do termo varia entre fornecedores — confirme no glossário interno.

## 5.3 Conectividade e fallback
```
Tenta conexão: Wi-Fi ─▶ (falha) ─▶ 4G ─▶ (falha) ─▶ 3G/GPRS ─▶ (falha) ─▶ MODO OFFLINE/queda
Leitura: chip/aproximação ─▶ (falha) ─▶ fallback para tarja (mais risco)
```
- **GPRS/3G/4G/Wi-Fi:** meios de conexão; **fallback** é o plano B quando o preferido falha.
- **Parâmetros de terminal:** tabelas de bandeiras, chaves, AIDs, configs — carregados na **carga de parâmetros**.
- **Ativação:** terminal ganha um **TID** (logical number) e fica apto.
- **TMS (Terminal Management System):** atualiza parâmetros/firmware **remotamente**.

## 5.4 Logística e operação de campo
- **Logística:** CD → transportadora → lojista; **swap** (troca) de máquina defeituosa.
- **Problemas comuns:** terminal sem sinal, parâmetro desatualizado, bobina/bateria, fila offline, venda duplicada por timeout.

## 5.5 Como queda de conexão afeta o negócio
```
Conexão cai no momento da autorização
   ├─▶ timeout: cliente acha que não passou e passa de novo → VENDA DUPLICADA
   ├─▶ venda não conclui → PERDA DE VENDA + lojista frustrado → risco de CHURN
   └─▶ comprovante sem confirmação → dúvida na conciliação
```

### Exemplo prático
Padaria em região com 4G ruim reclama de "máquina lenta". Diagnóstico: fallback indo para 3G + parâmetro velho. Ação de produto: **forçar Wi-Fi**, **atualizar via TMS** e **alertar automaticamente** terminais com latência alta.

### Onde IA ajuda
- Prever **terminais propensos a falha** (telemetria) e abrir manutenção proativa.
- Detectar **venda duplicada** por timeout e sugerir estorno automático.

### Perguntas de revisão
1. Diferença entre maquininha, PIN pad+TEF e gateway?
2. O que é fallback e por que ele aumenta risco?
3. Como queda de conexão vira venda duplicada?

---

# MÓDULO 6 — Subadquirência, Subcontas e Marketplaces

## 6.1 Conceitos
- **Subadquirente / subcredenciador:** agrega **muitos sub-ECs** sob seu contrato com uma adquirente. Faz onboarding, define taxa ao sub-EC, repassa.
- **Subcontas:** "contas lógicas" dentro do subadquirente, uma por seller, com sua agenda de recebíveis e saldo.
- **Marketplace:** caso clássico — a plataforma intermedeia pagamentos de muitos vendedores.

## 6.2 Quando usar modelo de subadquirente
Quando você é **plataforma/marketplace/SaaS** e precisa **pagar vários recebedores**, **controlar a experiência de pagamento** e **não quer** o custo regulatório/tecnológico de virar adquirente.

## 6.3 Lojista único × múltiplos recebedores
```
MODELO A — Lojista único:
  Todo o dinheiro entra no CNPJ da plataforma ─▶ depois ela paga os sellers "por fora"
  → simples, mas concentra risco fiscal/regulatório e some a rastreabilidade.

MODELO B — Múltiplos recebedores (split/subcontas):
  Cada venda já é dividida ─▶ cada seller tem sua subconta e agenda
  → rastreável, escalável, mas exige motor de split + KYC por seller.
```

## 6.4 Responsabilidades (quem responde por quê)
| Tema | Em geral, responsável |
|---|---|
| Cadastro/KYC dos sub-ECs | Subadquirente |
| Risco e monitoramento dos sub-ECs | Subadquirente (e a adquirente fica de olho no agregado) |
| Chargeback dos sub-ECs | Subadquirente (conforme contrato) |
| Liquidação ao seller | Subadquirente (recebe da adquirente e repassa) |

## 6.5 Como a adquirente enxerga um subadquirente
Como **um cliente grande e concentrado**: muito TPV numa conta só, com **risco agregado** (se o sub gerencia mal seus sellers, a adquirente herda o problema). Por isso a adquirente monitora **concentração, chargeback agregado e qualidade do onboarding** do sub.

> ⚠️ **Regulatório:** subcredenciadores acima de certos limiares de volume entram em obrigações específicas (inclusive liquidação centralizada). **[VALIDAR COM JURÍDICO/COMPLIANCE]** os limiares e exigências vigentes.

### Exemplo prático
Um marketplace de artesanato cresce e seus sellers começam a gerar chargeback. A adquirente vê o **chargeback agregado** subir e exige do sub **melhor KYC** e **reserva**. Produto: criar **score de risco por subconta** para o sub agir antes da adquirente bater na porta.

### Perguntas de revisão
1. Diferença entre vender como lojista único e com múltiplos recebedores?
2. O que são subcontas?
3. Por que a adquirente vê um subadquirente como "risco concentrado"?

---

# MÓDULO 7 — Split de Pagamento

## 7.1 O que é split
**Split** é **dividir o valor de uma transação** entre vários recebedores **na própria liquidação** (não "por fora").

## 7.2 Tipos e variações
| Tipo | Como divide |
|---|---|
| **Por percentual** | Cada recebedor recebe um % (ex.: 80/15/5). |
| **Por valor fixo** | Cada recebedor recebe um valor (ex.: R$50 fixos ao parceiro). |
| **Split bruto** | Divide o **valor cheio**; taxas debitadas depois (define-se de quem). |
| **Split líquido** | Divide o **valor já líquido** de taxas. |
| **Múltiplos recebedores** | 3+ destinos (vendedor, plataforma, parceiro logístico…). |

**Contextos:** marketplace (seller + plataforma), franquias (franqueado + franqueador + fundo de marketing), eventos (produtor + casa + plataforma), SaaS/plataformas (cliente + plataforma como taxa de serviço).

## 7.3 Quem paga taxa e quem assume chargeback
- **Taxa (MDR):** configurável — pode sair do total (split bruto) ou do recebedor principal.
- **Chargeback:** ponto sensível. Em geral recai sobre o **recebedor principal/plataforma** ou conforme regra contratual. **Decisão de produto crítica:** se você divide 100% e depois vem chargeback, **de quem você debita?** Tem que estar definido **antes**.

## 7.4 Exemplo — venda de R$1.000 com split
**Split bruto, percentual (vendedor 80% / plataforma 15% / parceiro 5%), MDR 3% pago pela plataforma:**
```
Venda............................. R$1.000,00
Split bruto:
   ├─ Vendedor (80%)............... R$  800,00
   ├─ Plataforma (15%)............. R$  150,00
   └─ Parceiro (5%)................ R$   50,00
MDR 3% (R$30) debitado da PLATAFORMA:
   └─ Plataforma líquida........... R$  120,00
Se vier chargeback de R$1.000 → quem devolve? (regra: vendedor + plataforma rateiam? só a plataforma?) ⚠️ defina!
```

## 7.5 Conciliar split
Cada recebedor tem **sua agenda e seu extrato**. Conciliação precisa **somar os splits = valor da venda** e **rastrear taxa e chargeback por recebedor**.

## 7.6 Como desenhar um motor de split
```
Regra de split (por venda) ─▶ Validação (soma = total; recebedores ativos/KYC ok)
   ─▶ Geração de recebíveis POR recebedor (agenda própria)
   ─▶ Tratamento de taxa (de quem)
   ─▶ Tratamento de estorno/chargeback (reversão proporcional?)
   ─▶ Idempotência (não dividir 2x a mesma venda)
   ─▶ Trilha de auditoria (quem recebeu o quê e por quê)
```
**Requisitos não-negociáveis:** idempotência, soma exata, reversão definida, auditoria.

### Perguntas de revisão
1. Diferença entre split bruto e líquido?
2. Por que "quem assume o chargeback no split" precisa estar definido antes?
3. Cite 3 requisitos de um motor de split confiável.

---

# MÓDULO 8 — Liquidação, Recebíveis e Antecipação

## 8.1 Recebíveis de arranjo de pagamento
Quando o EC vende no crédito, ele passa a ter um **direito de receber** no futuro — o **recebível de arranjo** (organizado em **URs**, unidades de recebíveis). Esse direito **tem valor** e pode ser **antecipado, dado em garantia ou cedido**.

## 8.2 Agenda de recebíveis e prazos
- **Agenda:** cronograma de **quanto** o EC recebe **em cada data**.
- **D+0/D+1/D+2/D+30:** dias úteis até a liquidação (D+0 = mesmo dia; D+30 = padrão do crédito à vista no BR).
```
Venda hoje (crédito à vista) ─▶ recebível em D+30
Venda parcelada 3x ─▶ recebíveis em D+30, D+60, D+90
```

## 8.3 Liquidação centralizada, domicílio e registradoras
- **Liquidação centralizada:** modelo (BACEN) em que os recebíveis são **registrados e liquidados de forma centralizada**, permitindo que **qualquer credor** (não só a adquirente) financie o EC contra seus recebíveis.
- **Instituição domicílio:** onde o EC recebe.
- **Registradoras de recebíveis:** **CERC, TAG (B3), Núclea/CIP** registram as URs. ⚠️ Confirme o conjunto vigente de registradoras e regras com **[JURÍDICO/COMPLIANCE]**.
- **Travas, garantias e cessão:** o EC pode **travar** recebíveis numa registradora para garantir um empréstimo, ou **ceder** para um financiador.

> **Por que isso mudou o jogo:** antes, só a sua adquirente antecipava seu recebível (mercado fechado). Com o registro/liquidação centralizada, **bancos e fintechs concorrem** para antecipar/financiar o EC → mais competição, margens de antecipação pressionadas.

## 8.4 Antecipação: automática × pontual
| Tipo | Como funciona | Quando |
|---|---|---|
| **Automática** | Todo volume é antecipado por padrão (taxa contratada) | EC quer caixa rápido sempre |
| **Pontual** | EC escolhe antecipar valores específicos sob demanda | EC com caixa, antecipa quando precisa |

## 8.5 Risco financeiro da antecipação
Você adianta dinheiro de uma venda que **pode ser cancelada, estornada ou contestada**. Riscos: **chargeback futuro, performance do EC, fraude**. Por isso antecipação pede **reserva, limites e monitoramento**.

## 8.6 Quem é afetado
```
Lojista: troca prazo por caixa, paga desconto (custo de capital)
Adquirente: ganha spread, assume risco de crédito do recebível
Bancos/fintechs: entram como financiadores via registradora
Mercado: mais concorrência → margem de antecipação cai com o tempo
```

## 8.7 Como explicar antecipação para um lojista (analogia)
> "É como **vender um cheque pré-datado**: você tem R$100 para receber daqui a 30 dias, mas precisa do dinheiro hoje. Eu te dou R$98 agora e fico com os R$100 lá na frente. Os R$2 são o preço de ter o dinheiro hoje."

### Exemplo numérico
EC tem R$10.000 a receber em D+30. Antecipa a ~2% → recebe ~**R$9.800 hoje**. A adquirente embolsa ~**R$200** de receita financeira (menos custo de funding e risco).

### Onde IA ajuda
- **Otimização de antecipação:** quanto/quando antecipar de cada EC para maximizar margem dentro do risco.
- Detecção de **EC que vai pedir antecipação** (oferta proativa) e de **risco de não-performance**.

### Perguntas de revisão
1. O que é uma UR e por que ela tem valor?
2. Por que a liquidação centralizada aumentou a concorrência na antecipação?
3. Qual o principal risco de antecipar um recebível de crédito?

---

# MÓDULO 9 — Backoffice de Adquirência

## 9.1 O que é
O **backoffice** é o conjunto de sistemas internos onde a adquirente **opera o dia a dia**: cadastra EC, analisa risco, gere POS, taxas, agenda, split, transações, chargeback, liquidação, conciliação, bloqueios e auditoria. É **onde mora a maior parte do trabalho manual** — e, portanto, o maior alvo de IA.

## 9.2 Quem usa
| Time | Usa o backoffice para |
|---|---|
| **Operações** | Cadastro, POS, ajustes operacionais |
| **Risco** | Underwriting, limites, reserva, bloqueios |
| **Comercial** | Taxas, propostas, acompanhamento de carteira |
| **Suporte/CS** | Resolver chamados de lojistas |
| **Financeiro** | Liquidação, conciliação, repasses |
| **Compliance** | KYC/KYB, PLD, auditoria |
| **Produto/Tech** | Construir e melhorar tudo acima |

## 9.3 Funcionalidades essenciais
Cadastro de EC · análise KYC/KYB · gestão de POS · gestão de taxas · agenda de recebíveis · split · acompanhamento de transações · chargeback · liquidação · conciliação · bloqueios/desbloqueios · **logs e auditoria**.

## 9.4 Como desenhar um bom backoffice
```
Princípios:
 1. Visão 360 do EC (cadastro + transações + agenda + risco + chamados em uma tela)
 2. Ações auditáveis (toda mudança logada: quem, quando, por quê)
 3. Permissões por papel (risco ≠ comercial ≠ suporte)
 4. Alertas em vez de relatórios passivos (o sistema avisa, não espera você procurar)
 5. Eventos, não telas: backoffice consome o mesmo event stream do core
```

## 9.5 Métricas e alertas que precisam existir
| Métrica | Alerta exemplo |
|---|---|
| TPV por EC/segmento | Queda abrupta de volume (churn iminente) |
| Taxa de aprovação | Aprovação caindo (problema técnico/risco) |
| Chargeback ratio | EC acima do limite de chargeback da bandeira |
| Liquidação | Liquidação ausente/atrasada |
| Reserva/limites | EC estourando limite |
| Conciliação | Divergências abertas acima de X |

## 9.6 Onde IA ajuda no backoffice
- **Copiloto interno**: "por que esse EC não foi liquidado hoje?" → resposta com fonte.
- Classificação/triagem de chamados e documentos.
- Explicação de divergências de conciliação em linguagem natural.
- Resumo automático do "estado do EC" para o time.

### Exemplo prático
CS perde 8 minutos por chamado garimpando 4 telas para explicar uma liquidação. Um **copiloto com RAG** sobre os dados do EC responde em segundos com **rastreabilidade** → reduz tempo de atendimento e erro.

### Perguntas de revisão
1. Cite 5 funcionalidades essenciais de um backoffice de adquirência.
2. Por que "ações auditáveis" é princípio inegociável?
3. Dê um exemplo de alerta que previne churn.

---

# MÓDULO 10 — Chargeback, Fraude e Risco

## 10.1 Conceitos
- **Chargeback:** estorno **forçado pelo portador** via emissor/bandeira.
- **Contestação (dispute):** o pedido formal que origina o chargeback.
- **Fraude real:** cartão roubado/clonado, terceiro mal-intencionado.
- **Fraude amigável (friendly fraud):** portador **legítimo** contesta uma compra que **realmente** fez ("não reconheço").
- **Autofraude:** o **próprio EC** fraudando (vendas falsas, conluio).

## 10.2 Tipos de risco
| Risco | O que é |
|---|---|
| **Risco de lojista** | EC específico com comportamento suspeito |
| **Risco de segmento (MCC)** | Setores naturalmente arriscados (viagem, infoproduto, eletrônicos, apostas) |
| **Risco de antecipação** | Recebível antecipado que pode não se concretizar |

## 10.3 Monitoramento e indicadores
```
Sinais: velocity (muitas vendas em pouco tempo) · ticket fora do padrão ·
        muitos cartões diferentes · geolocalização incoerente ·
        pico súbito de volume · concentração em horários estranhos
Indicadores: chargeback ratio (%), fraud ratio, taxa de negação,
             concentração por cartão/IP/dispositivo
```
**Regras por MCC:** limites e gatilhos diferentes por segmento. **Bloqueio de agenda:** segurar a liquidação de um EC suspeito. **Reserva de risco:** colchão para cobrir chargeback.

## 10.4 Ciclo completo de chargeback
```
1. Portador contesta no emissor
2. Emissor abre chargeback via bandeira ─▶ chega à adquirente
3. Adquirente debita/segura o valor do EC e notifica
4. EC apresenta defesa + evidências (representment)
5. Bandeira/emissor avaliam
   ├─ ganha o EC ─▶ valor devolvido
   └─ perde o EC ─▶ valor fica com o portador; pode ir a PRÉ-ARBITRAGEM / ARBITRAGEM
```

## 10.5 Onde IA ajuda (com cuidado)
- **Detecção de fraude:** scoring em tempo quase real (com humano para casos limítrofes).
- **Assistente de chargeback:** monta o **dossiê de defesa** automaticamente (nota fiscal, log de entrega, 3DS, histórico) e sugere se vale contestar.
- **Análise de risco de EC/segmento:** priorização de quem monitorar.

> ⚠️ Decisão de **bloquear EC** ou **negar transação** automaticamente toca **decisão automatizada** (LGPD) e relação com cliente → exige **explicabilidade, trilha de auditoria e revisão humana** onde apropriado. **[VALIDAR COM COMPLIANCE]**.

### Exemplo prático
Pico de vendas de madrugada num EC de eletrônicos, 30 cartões diferentes, tickets altos → score dispara → **bloqueio de agenda** + revisão humana. Evita antecipar dinheiro de vendas que viram chargeback.

### Perguntas de revisão
1. Diferença entre fraude real, amigável e autofraude?
2. O que é bloqueio de agenda e por que usá-lo?
3. Por que IA de bloqueio automático precisa de explicabilidade?

---

# MÓDULO 11 — Conciliação

## 11.1 O que é
**Conciliação** é **bater os registros** de uma mesma operação entre fontes diferentes para garantir que **tudo que foi vendido foi taxado e liquidado corretamente**.

## 11.2 Tipos de conciliação
Vendas · taxas · liquidação · chargeback · antecipação · split. Cada um responde a uma pergunta:
```
Vendas:       o que vendi bate com o que a adquirente registrou?
Taxas:        a taxa cobrada bate com a contratada?
Liquidação:   o que caiu no banco bate com o que era pra cair?
Chargeback:   os estornos foram identificados e debitados certo?
Antecipação:  o desconto cobrado bate com o contratado?
Split:        a soma dos recebedores bate com a venda?
```

## 11.3 As fontes (e por que divergem)
| Fonte | Visão |
|---|---|
| **Adquirente** | O que ela processou e vai liquidar |
| **Banco** | O que efetivamente caiu na conta |
| **ERP do lojista** | O que a loja registrou como venda |
| **Gateway** | O que passou no encanamento online |
| **Lojista (planilha)** | O que a pessoa anotou (sujeito a erro) |

## 11.4 Problemas comuns
Venda **duplicada** (timeout) · **valor divergente** · **taxa errada** · **liquidação ausente** · **chargeback não identificado** · diferença de **data** (D+x).

## 11.5 Como automatizar
```
Normalizar fontes ─▶ chave de matching (NSU, autorização, valor, data, bandeira)
   ─▶ casar 1:1 ─▶ aplicar tolerâncias ─▶ classificar divergências
   ─▶ sugerir causa provável ─▶ encaminhar para ação
```

## 11.6 Onde IA ajuda
- **Explicar divergências** em linguagem natural: *"a venda X aparece na adquirente mas não no banco porque a liquidação é D+1 e hoje é feriado"*.
- Aprender padrões de divergência recorrentes e **sugerir regra de matching**.
- Resumir "estado da conciliação do dia" para o financeiro.

### Exemplo prático
1.000 transações, 12 divergências. IA classifica: 6 são "liquidação D+1 ainda não caiu" (ok), 4 "taxa divergente" (erro de cadastro → abrir correção), 2 "chargeback não identificado" (debitar EC). Financeiro resolve em minutos, não horas.

### Perguntas de revisão
1. Por que adquirente, banco e ERP divergem mesmo quando tudo está certo?
2. Qual chave você usaria para casar uma venda presencial?
3. Como IA reduz o tempo de fechamento da conciliação?

---

# MÓDULO 12 — E-commerce, Gateway e APIs

## 12.1 Presencial × online
| | Presencial (CP) | Online (CNP) |
|---|---|---|
| Cartão | Físico (chip/aproximação) | Dados/token |
| Autenticação | Senha/biometria | 3DS, antifraude |
| Risco de fraude | Menor | **Maior** |
| Captura | POS | Gateway/checkout |

## 12.2 Componentes do mundo online
- **Gateway:** roteia a transação para a(s) adquirente(s).
- **Checkout transparente:** o cliente paga **sem sair** da loja (loja controla a UX; exige cuidado com PCI).
- **Tokenização:** troca o **PAN** por um **token** → loja não guarda número de cartão (reduz escopo PCI).
- **Cartão salvo (vault):** token guardado para próximas compras.
- **3DS (3-D Secure):** autentica o portador no emissor; pode **transferir a responsabilidade** do chargeback de fraude para o emissor (liability shift).
- **Antifraude:** score antes de autorizar.
- **Recorrência:** cobranças automáticas (assinaturas).

## 12.3 Boas práticas de API/arquitetura
- **Webhooks:** notificações assíncronas de eventos (com **assinatura** para autenticidade).
- **Idempotência:** `Idempotency-Key` para a **mesma requisição não cobrar 2x**.
- **Retentativas inteligentes (dunning):** recobrar falhas no melhor horário, evitando bloquear o cartão.
- **Roteamento multiadquirente (cascading):** se a adquirente A nega, tenta a B → **aumenta aprovação** e otimiza custo.

## 12.4 Eventos de uma arquitetura event-driven de pagamentos
```
payment.authorized ─▶ payment.captured ─▶ payment.settled
       │                    │                   │
       ▼                    ▼                   ▼
 payment.failed       payment.refunded    payout.created
                                                │
                              chargeback.opened ─▶ chargeback.resolved
                              antifraud.flagged   split.calculated
```

## 12.5 Como desenhar uma boa API de pagamento
```
1. Estados explícitos (authorized/captured/settled/refunded/charged_back)
2. Idempotência em toda escrita financeira
3. Webhooks assinados + retry com backoff
4. Tokenização nativa (nunca trafegar PAN à toa)
5. Erros acionáveis ("cartão sem limite" ≠ "tente outro cartão")
6. Versionamento e contratos estáveis
```

### Exemplo de decisão de produto
Aprovação online caindo num grande EC. Solução: **roteamento multiadquirente** + **retry inteligente** + **3DS seletivo** (só em transações de risco) → sobe aprovação **sem** estragar a UX.

### Perguntas de revisão
1. Por que idempotência é crítica em pagamentos?
2. O que o 3DS muda na responsabilidade por fraude?
3. Para que serve roteamento multiadquirente?

---

# MÓDULO 13 — Pix e Outros Meios

## 13.1 Pix no contexto da adquirente
O **Pix** é um arranjo do **BACEN**: pagamento **instantâneo, 24/7, liquidado em segundos**. Para a adquirente, Pix é **mais um meio de captura** (no POS e no checkout) e um **produto novo** ao lado do cartão.

## 13.2 Tipos de Pix
| Tipo | Uso |
|---|---|
| **Pix estático** | QR fixo (mesmo QR para vários pagamentos) — autônomo, mesa de bar |
| **Pix dinâmico** | QR por cobrança (valor/txid únicos) — checkout, conciliação fácil |
| **Pix cobrança** | Cobrança com vencimento/juros/multa (substitui boleto em muitos casos) |
| **QR Code / BR Code** | Padrão do QR brasileiro |

## 13.3 Pix × cartão (liquidação, risco, contestação)
| | Cartão (crédito) | Pix |
|---|---|---|
| Liquidação | D+1 a D+30 | ~D+0 (segundos) |
| Recebível antecipável | Sim | Não há "agenda" como no cartão |
| Contestação | **Chargeback** (estrutura da bandeira) | **Não tem chargeback**; é irrevogável. Disputa via **MED** (Mecanismo Especial de Devolução), focado em **fraude/golpe** |
| Risco principal | Fraude de cartão + chargeback | **Engenharia social/golpe** |

## 13.4 Conciliação de Pix
Por **txid** e **e2eid** (end-to-end ID). Mais simples que cartão (liquida na hora), mas precisa **casar com o pedido** e tratar **devoluções/MED**.

## 13.5 Pix em POS e checkout, e produtos combinados
- **Pix em POS:** QR na tela da maquininha (e pilotos de Pix por aproximação).
- **Pix no checkout:** QR dinâmico ou "Pix copia e cola".
- **Produtos cartão + Pix:**
  - **Pix Automático** para **recorrência** (alternativa a cartão recorrente).
  - **"Pix parcelado/crédito"** (financiamento em cima do Pix) — ⚠️ desenho e regras a **[VALIDAR COM COMPLIANCE]**.
  - **Roteamento de meio**: oferecer Pix quando o cartão nega (recuperar venda).

### Exemplo de produto
No checkout, se o cartão **nega**, oferecer **Pix na hora** → recupera a venda sem fricção. Métrica: **taxa de recuperação** por meio alternativo.

### Perguntas de revisão
1. Por que Pix não tem chargeback e o que é o MED?
2. Quando usar Pix estático × dinâmico?
3. Cite um produto que combina cartão + Pix.

---

# MÓDULO 14 — Regulação no Brasil (sem juridiquês, com precisão)

> ⚠️ **Leia isto primeiro:** este módulo dá o **mapa conceitual**. **Não tome nada aqui como norma literal.** Números de resolução, limiares e prazos **mudam** — sempre **[VALIDAR COM JURÍDICO/COMPLIANCE]**.

## 14.1 Papel do Banco Central
O **BACEN** regula e supervisiona **arranjos de pagamento** e **instituições de pagamento**. Define quem pode operar, regras de liquidação, registro de recebíveis e o Pix.

## 14.2 Conceitos-chave
- **Instituição de Pagamento (IP):** figura criada pela **Lei 12.865/2013** (marco dos arranjos e IPs). Tipos incluem **credenciadora**, **emissora de moeda eletrônica**, **emissora de instrumento pós-pago**, **iniciadora de pagamento**.
- **Arranjo de pagamento:** o conjunto de regras de um meio (ex.: o arranjo Visa crédito).
- **Credenciador × subcredenciador:** o credenciador habilita ECs no arranjo; o subcredenciador atua em cima, agregando sub-ECs.
- **Liquidação centralizada e recebíveis de cartão:** modelo que **centralizou a liquidação** e tornou os **recebíveis registráveis** em **registradoras** (CERC, TAG/B3, Núclea), permitindo trava/cessão a qualquer credor. ⚠️ Regras e datas exatas a validar.
- **Registradoras:** infraestrutura que registra as URs.
- **Open Finance e Pix:** iniciativas do BACEN que ampliam dados/meios (relevantes para produto).

## 14.3 Compliance e PLD/FT
- **PLD/FT** (Prevenção à Lavagem de Dinheiro e Financiamento ao Terrorismo): base legal na **Lei 9.613/1998** + regras do BACEN/COAF. Impacta **KYC/KYB, monitoramento e reportes**.
- **LGPD (Lei 13.709/2018):** proteção de dados pessoais. **Crítico para IA**: base legal de tratamento, minimização, e **direito à revisão de decisões automatizadas**.

## 14.4 O que isso significa para você (Head de AI)
```
Qualquer IA que:
  - use dados pessoais ─▶ pensar base legal LGPD + minimização
  - tome/influencie decisão sobre cliente ─▶ explicabilidade + revisão humana
  - toque dado de cartão ─▶ escopo PCI DSS
  - bloqueie/aprove EC ─▶ trilha de auditoria + alinhamento com risco/compliance
```
**Regra de ouro:** envolva **jurídico/compliance cedo** em qualquer projeto de IA que decida sobre dinheiro, crédito, bloqueio ou aceite.

### Perguntas de revisão
1. O que é uma IP e qual lei a institui?
2. Por que a LGPD importa especialmente para projetos de IA?
3. Quando você deve chamar compliance num projeto de IA?

---

# MÓDULO 15 — Players do Mercado

## 15.1 Mapa de players (Brasil + globais)
| Player | Tipo | Foco | Diferencial |
|---|---|---|---|
| **Cielo** | Adquirente tradicional (JV Bradesco/BB) | Varejo amplo | Escala, relacionamento bancário |
| **Rede** | Adquirente (Itaú) | Varejo + Itaú | Sinergia com banco |
| **Getnet** | Adquirente (Santander) | Varejo + Santander | Integração bancária |
| **Stone** | Fintech adquirente | PME/SMB | Distribuição "porta a porta", atendimento, software |
| **PagBank/PagSeguro** | Fintech (origem subadquirente) | Long tail, autônomos | Maquininha barata, banco digital |
| **Mercado Pago** | Fintech/marketplace | Sellers + consumo | Ecossistema Mercado Livre |
| **SafraPay** | Banco (Safra) | Clientes do banco | Relacionamento private/empresas |
| **Adyen** | Global enterprise | Grandes/global | Plataforma única (unified commerce) |
| **Stripe** | Global, dev-first | Online/SaaS | Excelência de API/DX |
| **Global Payments / Fiserv / Worldpay** | Processadoras globais | Infra/enterprise | Escala de processamento |
| **EVO Payments** | Adquirente (adquirida pela Global Payments) | Internacional | — |

## 15.2 Como comparar players (eixos)
```
Público:        SMB ◀──────────────▶ Enterprise
Canal:          Presencial ◀────────▶ Online/Omni
Tecnologia:     Maquininha ◀────────▶ API/Plataforma
Distribuição:   Bancária ◀──────────▶ Direta/Digital
Posicionamento: Preço ◀─────────────▶ Relacionamento/Serviço
```

## 15.3 Modelos de negócio
- **Adquirente tradicional bancária:** escala + base do banco.
- **Fintech adquirente:** distribuição agressiva + software + serviços financeiros.
- **Subadquirente/marketplace:** monetiza o ecossistema (split, banco, crédito).
- **Gateway/processadora:** vende encanamento/tecnologia.
- **Global (Adyen/Stripe):** plataforma única para grandes contas.

### Exemplo de leitura estratégica
Stone e PagBank cresceram **fora** dos bancos, atacando **SMB** com **maquininha + banco digital + crédito**; Cielo/Rede/Getnet defendem com **base bancária**; Adyen/Stripe miram **enterprise/online**. Onde sua adquirente joga define **onde IA tem mais ROI** (ex.: SMB → atendimento/churn; enterprise → aprovação/roteamento).

### Perguntas de revisão
1. Diferença de modelo entre Cielo e Stone?
2. Por que Adyen/Stripe focam enterprise/online?
3. Como o posicionamento do player muda a prioridade de IA?

---

# MÓDULO 16 — Tecnologia de uma Adquirente

## 16.1 Arquitetura de alto nível
```
                 ┌─────────────────────────────────────────────┐
  Captura ──────▶│  CORE TRANSACIONAL (autorizador / switch)   │──▶ Bandeiras/Processadoras
 (POS/Gateway)   │     baixa latência, alta disponibilidade    │
                 └───────┬──────────────────────────┬──────────┘
                         │ eventos                   │
              ┌──────────▼──────────┐     ┌──────────▼───────────┐
              │  MOTOR DE REGRAS    │     │     LEDGER (razão)    │  ◀ fonte da verdade financeira
              │ (risco/roteamento)  │     │  double-entry, imutável│
              └──────────┬──────────┘     └──────────┬───────────┘
        ┌────────────────┼─────────────────┬─────────┼──────────────┐
   ┌────▼────┐     ┌─────▼─────┐     ┌──────▼────┐ ┌──▼─────────┐ ┌──▼──────────┐
   │ SPLIT   │     │ANTECIPAÇÃO│     │LIQUIDAÇÃO │ │CONCILIAÇÃO │ │ GESTÃO POS  │
   │ engine  │     │ engine    │     │ (D+x)     │ │            │ │ (TMS)       │
   └─────────┘     └───────────┘     └───────────┘ └────────────┘ └─────────────┘
                         │
              Observabilidade · Logs/Auditoria · Segurança (PCI/Tokenização)
```

## 16.2 Componentes
- **Core transacional / autorizador / switch:** processa autorizações em ~ms; **HA e baixa latência são lei**.
- **Ledger:** razão contábil **double-entry**, imutável — **fonte da verdade** do dinheiro.
- **Motor de regras:** risco, roteamento, decisões.
- **Motor de split / antecipação / liquidação / conciliação:** os "engines" financeiros.
- **Gestão de POS (TMS):** parâmetros, ativação, atualização remota.
- **Integração com bandeiras/processadoras:** protocolos específicos.
- **Observabilidade, logs e auditoria:** rastreabilidade ponta a ponta.
- **Segurança: PCI DSS, tokenização, criptografia.**

## 16.3 Pensando para escala e resiliência
```
Princípios:
 - Idempotência em tudo que move dinheiro
 - Event sourcing / event-driven (replay, auditoria, desacoplamento)
 - O ledger é sagrado: nunca "edita", só lança contrapartida
 - Degradação graciosa (autorizar mesmo com periféricos lentos)
 - Observabilidade: métrica de aprovação, latência, erro por adquirente/bandeira
```

> **Para você:** **não coloque IA no caminho crítico de autorização** sem necessidade — latência e disponibilidade são sagradas. IA brilha **ao lado** do core (risco assíncrono, copiloto, conciliação, churn), não **dentro** do switch de 50ms, salvo casos muito específicos e bem isolados.

### Perguntas de revisão
1. Por que o ledger nunca é "editado"?
2. Por que evitar IA no caminho crítico de autorização?
3. O que torna idempotência essencial na arquitetura?

---

# MÓDULO 17 — AI Aplicada à Adquirência

## 17.1 Onde IA gera valor real (mapa por área)
| Área | Aplicação de IA | Tipo de ganho |
|---|---|---|
| Atendimento/CS | Atendimento automatizado a lojistas | Custo + satisfação |
| Operações | Copiloto interno (RAG na doc/dados) | Produtividade |
| Conciliação | Explicação automática de divergências | Tempo + erro |
| Liquidação/taxas | Explicar liquidação/taxa ao EC e ao CS | Tempo + confiança |
| Risco/Fraude | Detecção e scoring | Perda evitada |
| Chargeback | Assistente de defesa/dossiê | Recuperação financeira |
| Comercial/CS | Predição de churn, priorização | Receita retida |
| Roteamento | Roteamento inteligente multiadquirente | Aprovação ↑ |
| Pricing | Precificação dinâmica/assistida | Margem |
| Onboarding | Classificação de MCC, underwriting assistido | Velocidade + risco |
| Suporte/voz | Análise de chamadas e tickets | Insight + qualidade |

## 17.2 Como priorizar por ROI
```
Pontue cada ideia:
  Impacto financeiro × Frequência × Viabilidade de dados ÷ Risco regulatório
Comece por: alto impacto, dados já existentes, baixo risco regulatório.
(Ex.: copiloto interno e explicação de conciliação ganham de "underwriting 100% automático" no começo.)
```

## 17.3 Riscos de usar IA em pagamentos
- **Alucinação:** resposta errada sobre dinheiro = grave. Mitigue com **RAG + citação de fonte + "não sei"**.
- **Dados sensíveis:** **PAN/PCI** e dados pessoais (**LGPD**). Minimize, mascare, isole.
- **Decisão automatizada:** bloqueio/negação sobre cliente exige **explicabilidade + revisão humana**.
- **Auditoria:** toda decisão influenciada por IA precisa de **trilha** (input, versão do modelo, output, quem decidiu).

> **Princípio:** **IA propõe, processo decide, humano responde** — especialmente em risco, crédito e bloqueio. **[VALIDAR COM COMPLIANCE]** os limites do automatizável.

### Perguntas de revisão
1. Por que copiloto interno costuma ter ROI melhor que "underwriting 100% automático" no início?
2. Como mitigar alucinação num assistente que explica liquidação?
3. O que precisa estar na trilha de auditoria de uma decisão com IA?

---

# MÓDULO 18 — Roadmap V1/V2/V3 (Produto + AI)

## V1 — Fundacional (organizar a casa e provar valor barato)
```
Objetivo: base de dados/documentação confiável + primeiros copilotos de baixo risco.
 - Organizar documentação interna (políticas, fluxos, regras de bandeira)
 - Mapear fluxos: transação, liquidação, chargeback, conciliação
 - Criar GLOSSÁRIO INTERNO (termos, siglas, donos de cada área)
 - Copiloto interno simples com RAG (sobre doc + dados de EC)
 - Automatizar FAQ de suporte (top 20 dúvidas de lojista)
 - Dashboards básicos: TPV, aprovação, churn, chargeback, liquidação
Critério de sucesso: tempo de atendimento ↓, perguntas respondidas com fonte, doc viva.
```

## V2 — Operacional (atacar trabalho manual com risco controlado)
```
Objetivo: reduzir retrabalho e perda nas operações.
 - Copiloto para BACKOFFICE (operações/CS/financeiro)
 - Explicação automática de divergências de conciliação
 - Classificação/triagem de tickets
 - Assistente de chargeback (monta dossiê, sugere contestar)
 - Alertas de risco (velocity, chargeback ratio, queda de TPV)
 - Motor de insights para comercial/CS
 - Priorização de lojistas com queda de volume (anti-churn)
Critério de sucesso: divergências resolvidas mais rápido, recuperação de chargeback ↑, churn ↓.
```

## V3 — Inteligência avançada (decisão assistida, sempre auditável)
```
Objetivo: alavancar margem e risco com IA, com governança forte.
 - Roteamento inteligente multiadquirente (aprovação ↑)
 - Precificação assistida (margem por EC/segmento)
 - Underwriting assistido por IA (com revisão humana)
 - Predição de churn (com ação automática de retenção)
 - Detecção avançada de fraude
 - Otimização de antecipação (quanto/quando, dentro do risco)
 - Agentes especializados por área
 - AI observability + auditoria de decisões
Critério de sucesso: ganho de margem/aprovação mensurável + governança/auditoria sólidas.
```

> **Sequência intencional:** V1 cria **confiança e dados**, V2 cria **eficiência**, V3 cria **vantagem competitiva**. Pular para V3 sem V1/V2 é receita para alucinação, retrabalho e atrito com risco/compliance.

---

# MÓDULO 19 — Plano de 30/60/90 dias para o Head de AI

## Primeiros 30 dias — Entender (não construir nada irreversível)
```
APRENDER:   fluxos de transação, liquidação, chargeback, conciliação, antecipação
ENTREVISTAR: produto, engenharia, operações, risco, compliance, financeiro, comercial, CS, dados, POS
PEDIR DADOS: TPV, aprovação, churn, chargeback ratio, agenda de recebíveis, tickets de suporte
DASHBOARDS:  o que existe? o que é "fonte da verdade"? o que é planilha manual?
DOCUMENTOS:  políticas de risco, contratos de bandeira/adquirente, manuais de backoffice
SISTEMAS:    core, ledger, backoffice, TMS, conciliação, data warehouse
RISCOS A EVITAR: prometer automação de decisão de risco/crédito antes de entender governança
```

## 30–60 dias — Provar (quick wins de baixo risco)
```
QUICK WINS: copiloto interno com RAG, FAQ de suporte, explicação de conciliação
NÃO FAZER AINDA: underwriting automático, bloqueio automático, pricing automático
PRIMEIRO PROJETO: escolher por (impacto × frequência × dados prontos × baixo risco)
MEDIR: baseline ANTES (tempo de atendimento, divergências, recuperação de chargeback)
```

## 60–90 dias — Escalar e comunicar
```
ESCALAR: transformar o quick win em produto interno com dono e métricas
COMUNICAR À DIRETORIA:
   - ANTES vs DEPOIS em número (R$ economizados, tempo, churn, recuperação)
   - linkar a métrica de negócio (TPV retido, margem, custo operacional)
   - mostrar governança (LGPD/PCI/auditoria) — diretoria de adquirente teme risco
GOVERNANÇA: definir trilha de auditoria e política de uso de IA com compliance
```

### Como escolher o primeiro projeto de AI
```
Bom 1º projeto = alto volume de trabalho manual + dados já disponíveis + baixo risco regulatório + dono claro
Ex.: explicação de conciliação OU copiloto de CS.
Mau 1º projeto = decisão automatizada sobre crédito/bloqueio (alto risco, baixa confiança inicial).
```

### Como medir impacto
- **Operacional:** tempo por tarefa, tickets resolvidos sem humano, divergências/dia.
- **Financeiro:** R$ recuperados em chargeback, churn evitado (TPV retido), margem de antecipação.
- **Confiança:** taxa de resposta com fonte, taxa de "não sei" apropriada, incidentes.

---

# MÓDULO 20 — Perguntas Internas por Área

> Use estas perguntas nas entrevistas dos primeiros 30 dias. O objetivo é mapear **como funciona hoje, onde dói, onde há retrabalho manual, onde se decide no feeling, onde se perde dinheiro/lojista, onde há risco regulatório e onde IA ajudaria sem criar risco**.

### Produto
- Qual o roadmap atual e quais métricas de negócio ele move (TPV, churn, margem)?
- Quais decisões de produto hoje são tomadas sem dado?
- Onde os lojistas mais reclamam? Qual a maior dor não resolvida?

### Engenharia
- Como é a arquitetura do core, ledger e backoffice? O que é fonte da verdade?
- O que é event-driven e o que ainda é batch/manual?
- Onde estão os gargalos de latência/aprovação? Como medimos?
- Que dados existem e onde (data warehouse, logs, eventos)?

### Operações
- Quais tarefas consomem mais horas manuais? (onboarding, ajustes, conciliação?)
- Onde há retrabalho recorrente? Quantas telas para resolver um caso típico?
- Quais erros operacionais mais geram perda ou retrabalho?

### Risco
- Como funciona o underwriting hoje? O que é regra, o que é feeling?
- Quais MCCs/segmentos mais geram chargeback e perda?
- Como definimos reserva, limite e bloqueio? É auditável?
- Onde a antecipação mais nos expõe?

### Compliance
- Quais decisões **não** podem ser automatizadas hoje? Por quê?
- Quais obrigações de PLD/FT e LGPD impactam uso de dados/IA?
- O que precisa de trilha de auditoria e revisão humana?

### Financeiro
- Como fechamos a conciliação? Quanto tempo leva? Onde trava?
- Quais divergências são recorrentes e quanto custam?
- Como apuramos margem por EC/segmento/produto?

### Comercial
- Como precificamos lojistas? Onde perdemos margem por desconto sem critério?
- Quais lojistas estão em risco de churn e como sabemos?
- O que o time gostaria de saber sobre a carteira e não consegue hoje?

### Atendimento (CS)
- Quais são os top 20 motivos de chamado? Quais são repetitivos?
- Quanto tempo leva um chamado típico e o que torna lento?
- Onde a falta de informação rápida vira lojista insatisfeito?

### Dados
- Qual a qualidade/governança dos dados? O que é confiável?
- Temos os eventos de transação/liquidação/chargeback acessíveis?
- O que falta para treinar/avaliar modelos com segurança (PCI/LGPD)?

### Logística/POS
- Quais terminais falham mais e por quê (conectividade, parâmetro, hardware)?
- Como é o ciclo de envio/troca/ativação? Onde atrasa?
- Conseguimos prever falha e agir antes do lojista reclamar?

### Perguntas transversais (faça a todos)
- "Se você pudesse apagar **uma** tarefa manual da sua semana, qual seria?"
- "Onde a gente **perde dinheiro** sem perceber?"
- "Onde a gente **perde lojista**?"
- "Que decisão você toma **no feeling** que adoraria ter dado embasando?"

---

<a name="casos"></a>
## CASOS REAIS SIMULADOS

### Caso 1 — A venda fantasma (timeout)
**Situação:** lojista jura que vendeu R$450, cliente diz que pagou, mas não cai na conta. **Causa:** queda de 4G no momento da autorização → cliente passou 2x → **uma venda duplicada e uma órfã**. **Conciliação** acusa divergência. **Ação:** detectar duplicidade por NSU/valor/horário, estornar a duplicada, explicar ao lojista. **IA:** detector de duplicidade + explicação automática. **Lição:** conectividade vira problema financeiro e de confiança.

### Caso 2 — O infoproduto que explodiu de madrugada
**Situação:** EC de curso online, MCC de risco, dispara R$80 mil em 3h de madrugada, 60 cartões. **Risco:** fraude real + chargeback futuro; pior, **antecipação** já programada. **Ação:** **bloqueio de agenda** + revisão humana + reserva. **IA:** score de anomalia (velocity + horário + cartões distintos) com **explicabilidade**. **Lição:** antecipar dinheiro de venda suspeita é como pagar adiantado por algo que pode não existir.

### Caso 3 — Marketplace e o split que ninguém definiu
**Situação:** marketplace divide 100% entre seller (90%) e plataforma (10%); vem chargeback de R$2.000 de uma venda do seller. **Problema:** ninguém definiu **de quem debitar** — seller já sacou. **Ação:** política de **reserva por subconta** + regra de reversão proporcional + KYC melhor. **IA:** risco por subconta. **Lição:** "quem assume o chargeback no split" precisa estar resolvido **no desenho**, não na crise.

### Caso 4 — A taxa errada que vazou margem por meses
**Situação:** conciliação de taxas mostra que 1.200 ECs estavam com **MDR de débito** aplicado em **crédito** por erro de cadastro. **Impacto:** margem perdida silenciosamente. **Ação:** corrigir cadastro + auditoria + alerta. **IA:** explicação/identificação de divergência de taxa em escala. **Lição:** conciliação não é burocracia — é **defesa de margem**.

### Caso 5 — O lojista que ia embora (e ninguém viu)
**Situação:** grande EC com TPV caindo 40% em 3 semanas; ninguém percebeu até ele pedir cancelamento. **Causa:** concorrente ofereceu antecipação mais barata. **Ação:** **alerta de queda de TPV** + ação comercial proativa + revisão de pricing/antecipação. **IA:** predição de churn + priorização. **Lição:** o sinal estava nos dados; faltou o alerta.

---

<a name="checklist"></a>
## CHECKLIST — O que você precisa saber para começar bem

**Negócio**
- [ ] Sei explicar, sem olhar, como a adquirente ganha dinheiro (MDR, antecipação, POS, serviços).
- [ ] Sei diferenciar adquirente, subadquirente, gateway, bandeira, emissor, processadora, IP, EC e instituição domicílio.
- [ ] Sei decompor o MDR (interchange + scheme fee + net MDR) com um exemplo de R$100.
- [ ] Entendo por que antecipação é (frequentemente) a maior margem e seu risco.

**Fluxos**
- [ ] Desenho o fluxo de transação (autorização → captura → clearing → liquidação → repasse).
- [ ] Diferencio autorizada/capturada/liquidada/cancelada/estornada/contestada.
- [ ] Entendo presencial (CP) × online (CNP) e por que CNP tem mais fraude.

**Operação e risco**
- [ ] Entendo onboarding, KYC/KYB, MCC/CNAE, underwriting, limites e reserva.
- [ ] Entendo o ciclo de chargeback e o que é bloqueio de agenda.
- [ ] Entendo conciliação e as fontes que divergem.
- [ ] Entendo split (bruto/líquido, %/fixo, quem paga taxa, quem assume chargeback).

**Liquidação e recebíveis**
- [ ] Entendo agenda, D+x, liquidação centralizada, registradoras, trava/cessão.
- [ ] Sei explicar antecipação para um lojista com uma analogia.

**Pix e online**
- [ ] Entendo Pix (estático/dinâmico, sem chargeback, MED) e como entra no POS/checkout.
- [ ] Entendo gateway, tokenização, 3DS, idempotência, webhooks, roteamento multiadquirente.

**Tecnologia e IA**
- [ ] Entendo a arquitetura (core, ledger, engines, TMS, observabilidade, PCI).
- [ ] Sei por que **não** colocar IA no caminho crítico de autorização sem necessidade.
- [ ] Tenho um mapa de onde IA gera valor e como priorizar por ROI.
- [ ] Sei os riscos de IA em pagamentos (alucinação, PCI/LGPD, decisão automatizada, auditoria).

**Governança**
- [ ] Sei quando chamar jurídico/compliance.
- [ ] Tenho as perguntas internas por área prontas para os primeiros 30 dias.

---

<a name="fontes"></a>
## FONTES, DOCUMENTOS E REGULAÇÃO PARA ESTUDAR DEPOIS

> ⚠️ Confirme versões/vigência com **jurídico/compliance**. Não decore números; saiba **onde** procurar.

**Regulação e instituições (Brasil)**
- **Banco Central do Brasil (bcb.gov.br):** arranjos de pagamento, instituições de pagamento, Pix, liquidação centralizada, recebíveis.
- **Lei 12.865/2013:** marco de arranjos e instituições de pagamento.
- **Lei 9.613/1998 (PLD/FT)** e regras BACEN/COAF correlatas.
- **LGPD — Lei 13.709/2018** (e materiais da ANPD), com atenção a **decisão automatizada**.
- **Resoluções/Instruções Normativas do BACEN** sobre liquidação centralizada e registro de recebíveis — **identificar as vigentes**.
- **Registradoras de recebíveis:** CERC, TAG (B3), Núclea — documentação técnica de registro/trava.

**Bandeiras e segurança**
- **Visa / Mastercard / Elo:** regras de arranjo, MCC, interchange, disputa/chargeback (manuais de bandeira).
- **PCI DSS:** padrão de segurança de dados de cartão (PCI Security Standards Council).
- **EMVCo:** especificações de chip/contactless e tokenização.

**Documentos internos a solicitar (na empresa)**
- Política de risco e underwriting; matriz de MCC × risco.
- Tabelas de MDR/interchange/scheme fee por bandeira/produto.
- Contratos com bandeiras/processadoras e com sua adquirente patrocinadora (se subadquirente).
- Manuais do backoffice, TMS e fluxos de conciliação/liquidação.
- Política de PLD/FT, LGPD e de uso de IA (se existir).

**Aprofundamento técnico/produto**
- Documentação pública de APIs de **Stripe** e **Adyen** (referência de bom design de API de pagamentos).
- Materiais sobre **ledger/double-entry** e arquitetura **event-driven** para fintech.

---

<a name="cheatsheet"></a>
# CHEAT SHEET (2 páginas) — Para Memorizar

### PÁGINA 1 — Atores, dinheiro e fluxo

**QUEM É QUEM (decore o teste: "quem assume chargeback + quem liquida?")**
```
Adquirente/Credenciador → habilita EC, captura, liquida, assume risco
Subadquirente           → agrega sub-ECs sobre uma adquirente, assume risco dos seus
Gateway                 → só roteia (não liquida, não assume risco)
Processadora            → switch técnico (não assume risco)
Bandeira                → regras + interchange/scheme fee (Visa/Master/Elo)
Emissor                 → emite cartão, banca o portador (Nubank/Itaú)
EC                      → o lojista (paga MDR, traz risco)
Instituição domicílio   → onde o EC recebe
```

**FLUXO DA TRANSAÇÃO**
```
Captura → Autorização (POS→Adq→Proc→Bandeira→Emissor) → Resposta → Captura/Confirmação
        → Clearing (lote) → Settlement/Liquidação (D+x) → Repasse ao EC (líquido de MDR)
Estados: Autorizada → Capturada → Liquidada | (Cancelada / Estornada / Contestada)
```

**ECONOMIA (venda R$100 crédito, MDR 2,5%)**
```
MDR 2,5% = R$2,50 = Interchange (~R$1,30 → emissor) + Scheme (~R$0,13 → bandeira) + Net MDR (~R$1,07 → adquirente)
EC recebe R$97,50 em D+30 (ou D+1 se antecipar; desconto vira receita financeira da adquirente)
Net MDR / Take rate = o que sobra para a adquirente
```

**3 FONTES DE RECEITA:** MDR · Antecipação (maior margem) · POS/Serviços
**3 ALAVANCAS DE MARGEM:** Volume (TPV) · Risco · Prazo de liquidação

**PRODUTOS:** Débito (D+1, risco baixo) · Crédito à vista (D+30) · Parcelado lojista (D+30,60,90 → ótimo p/ antecipação)

---

### PÁGINA 2 — Risco, recebíveis, Pix, tech e IA

**ONBOARDING & RISCO**
```
KYC (pessoa) + KYB (empresa) → MCC (bandeira) vs CNAE (fiscal) → Underwriting → Limite + Reserva
Chargeback: portador→emissor→bandeira→adquirente→EC (defesa/representment→pré-arbitragem→arbitragem)
Fraude: real (3º) · amigável (portador legítimo nega) · autofraude (EC)
Defesas: monitoramento (velocity/ticket/cartões) · bloqueio de agenda · reserva (rolling reserve)
Pix NÃO tem chargeback → disputa via MED (foco golpe)
```

**RECEBÍVEIS & ANTECIPAÇÃO**
```
Venda crédito → recebível (UR) na agenda (D+x)
Liquidação centralizada + Registradoras (CERC/TAG/Núclea) → trava/cessão a qualquer credor
Antecipação: automática (sempre) ou pontual (sob demanda); risco = chargeback/performance/fraude
Pitch ao lojista: "vender o cheque pré-datado": R$100 em D+30 → R$98 hoje
```

**SPLIT**
```
Bruto (valor cheio) vs Líquido (já sem taxa) · por % ou valor fixo
DEFINIR SEMPRE: quem paga taxa + quem assume chargeback
Motor: validação (soma=total) · recebível por recebedor · idempotência · reversão · auditoria
```

**CONCILIAÇÃO** = bater Adquirente × Banco × ERP × Gateway. Erros: duplicada · valor · taxa · liquidação ausente · chargeback não id.

**TECH (regra de ouro)**
```
Core/Switch (ms, HA) + Ledger (double-entry, imutável, fonte da verdade) + Engines (split/antecip/liq/concil) + TMS (POS)
Idempotência em tudo que move dinheiro · event-driven · PCI/tokenização
⚠️ NÃO botar IA no caminho crítico de autorização sem necessidade
```

**IA — ONDE GANHAR (ordem de ROI no começo)**
```
1) Copiloto interno (RAG) · explicação de conciliação/liquidação · FAQ de suporte   [V1]
2) Assistente de chargeback · classificação de tickets · alertas de risco · anti-churn [V2]
3) Roteamento · pricing assistido · underwriting assistido · fraude avançada · antecipação [V3]
RISCOS: alucinação (RAG+fonte+"não sei") · PCI/LGPD (mascarar/isolar) · decisão automatizada (humano+auditoria)
PRINCÍPIO: IA propõe · processo decide · humano responde
```

**REGRA DE OURO REGULATÓRIA:** chamou dinheiro, crédito, bloqueio ou dado pessoal → **valide com jurídico/compliance antes**.

---

*Fim do curso. Use os módulos como referência, o Módulo 20 nas suas primeiras reuniões e o cheat sheet para fixar. Bem-vindo ao mundo da adquirência.*
