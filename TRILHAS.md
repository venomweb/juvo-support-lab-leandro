# Registro de Análise de Incidentes (Troubleshooting)

---

## IT-1001 — Desembolso ao cliente – 901.111.111-01

**1. Onde consulto primeiro**
Sistema interno → contrato 80001001 / CPF 901.111.111-01 → coluna 'contract_status'

**2. O que busco**
Verificar o status do contrato para verificar se existe alguma mensagem específica.

**3. O que encontrei**
CSV: `contract_status=assinado`, `disbursement_error=TIMEOUT_BANCARIZADOR`, `last_disbursement_attempt=2026-06-11T06:45:00Z`, `credit_status=aprovado_alpha9`.

**4. Hipótese**
Erro na conexão (possivelmente no endpoint) com o BANCARIZADOR ou tratamento de resposta específica.

**5. Retry/reprocesso**
Precisaria testar o nosso lado para confirmar se há algum erro de conexão ou até mesmo o tratamento da resposta do BANCARIZADOR. Verificaria se existe alguma ferramenta disponível para teste ou se nesse caso é necessário escalar.

**6. Correção manual**
Nesse caso, no meu entendimento, precisamos de validação/solicitação externa e por isso a única forma de tentativa da correção é executar manualmente alguma função para validação/solicitação com o BANCARIZADOR (vide acima).

**7. Escalação**
Escala somente se o teste manual falhar ou erro repetir em outros clientes. Dependendo do tipo de erro escalar ao DEV ou BANCARIZADOR.

**8. Comunicação**
`@agente.cx01` Erro na tentativa de pagamento do desembolso pelo BANCARIZADOR. Verificando se consigo efetuar o teste/validação através das ferramentas disponíveis. Orientar ao cliente que estamos analisando e retornaremos assim que possível.

**9. Status final**
ESCALADO

---

## IT-1002 — Onboarding site (Continuar cadastro pela área logada) - 901.222.222-02

**1. Onde consulto primeiro**
Sistema interno → contrato - / CPF 901.222.222-02 → coluna 'onboarding_error'

**2. O que busco**
Verificar a mensagem do onboarding_error para verificar e reportar o erro específico.

**3. O que encontrei**
CSV: `onboarding_step=bank_account`, `onboarding_error=KYC_BANK_MISMATCH`.

**4. Hipótese**
KYC (Know your customer) é a validação de identidade obrigatória. No meu entendimento, os dados do contrato estão em conflito com os dados da conta utilizada para o desembolso. Pedir para o cliente verificar se ele está utilizando uma conta vinculada a ele (mesmo CPF).

**5. Retry/reprocesso**
N/A. Necessária ação do cliente – validação dos dados da conta destino.

**6. Correção manual**
N/A. Caso seja parte do trabalho do CX ou do nosso time, pedir os dados da conta e atualizar o sistema.

**7. Escalação**
N/A. Primeiramente precisa validar os dados do contrato vs conta do cliente.

**8. Comunicação**
`@agente.cx02` Ao que tudo indica, ocorreu um problema na validação da conta do cliente e os dados do contrato. Validar com o mesmo se ele está utilizando sua própria conta do banco (mesmo CPF).

**9. Status final**
SOLUCIONADO

---

## IT-1003 — Pagamento do cliente – 901.333.333-03

**1. Onde consulto primeiro**
Sistema interno → contrato 80003001 / CPF 901.333.333-03 → colunas parcelas e extrato do Banco

**2. O que busco**
Parcela 8 em aberto? Pagamento com mesmo valor/data do comprovante do CX?

**3. O que encontrei**
CSV: `payment_status=pago`, `payment_id=banco_pay_fict_001`, R$ 403,06 em 10/06 17:32, mas `installment_status=aberta`.

**4. Hipótese**
PIX compensado no Banco; webhook ou baixa automática não refletiu no sistema interno.

**5. Retry/reprocesso**
Tentaria reprocessar webhook/baixa automática se existir botão ou job — uma vez.

**6. Correção manual**
Se retry falhar ou não existir: **baixar parcela 8** manualmente no sistema interno vinculando ao `banco_pay_fict_001`.

**7. Escalação**
N/A neste caso — pagamento identificado, ação operacional no suporte. Escala só se baixa manual falhar ou erro repetir em outros clientes.

**8. Comunicação**
`@agente.cx03` Pagamento localizado no Banco (banco_pay_fict_001). Parcela 8 baixada no sistema interno. Pode orientar o cliente.

**9. Status final**
SOLUCIONADO

_Utilizada a resposta enviada no modelo TRILHA._ 

---

## IT-1004 — Erro ao gerar parcela(s) cobrança (sistema interno) - 901.444.444-04

**1. Onde consulto primeiro**
Sistema interno → contrato 80004001 / CPF 901.444.444-04 → colunas 'last_doc_generated', 'last_doc_type'

**2. O que busco**
Confirmação a respeito do último boleto gerado.

**3. O que encontrei**
CSV: `last_doc_generated=doc_fict_0401`, `last_doc_type=quitacao`, `last_doc_status=ativa`, `installment_ref=parcela_6`, `installment_status=aberta`.

**4. Hipótese**
De fato foi gerado um boleto de quitação ao invés da parcela 6 que está em aberto.

**5. Retry/reprocesso**
Efetuaria o cancelamento do boleto de quitação e geraria um novo boleto apenas com a parcela 6.  
> **IMPORTANTE:** Também é necessário verificar o vencimento do boleto, pois pode ser que o cliente esteja tentando postergar o pagamento gerando solicitações de boletos diferentes. Também é necessário entender o procedimento para esses casos – entender a política interna sobre cancelar boletos, gerar novos boletos, vencimentos e o que fazer em caso de solicitar novo boleto de um boleto já vencido etc.

**6. Correção manual**
Efetuar o cancelamento do boleto atual (`doc_fict_0401`) e gerar um novo apenas da parcela 6. Atualizar os campos necessários para refletir essa alteração.

**7. Escalação**
N/A. Escala apenas se o cancelamento e/ou geração do novo boleto acusar erro.

**8. Comunicação**
`@agente.cx04` Confirmado que foi gerado um boleto de quitação ao invés da parcela 6. Estou efetuando o procedimento para cancelar o boleto de quitação e reemitir o boleto da parcela. Aguardar retorno.

**9. Status final**
EM ANÁLISE

---

## IT-1005 — Erro ao gerar negociação (Juvo Negocia) - 901.555.555-05

**1. Onde consulto primeiro**
Sistema interno → contrato 80005001 / CPF 901.555.555-05 → coluna 'renegotiation_block_reason'

**2. O que busco**
Verificar o motivo que está acusando na tentativa de renegociação.

**3. O que encontrei**
CSV: `renegotiation_block_reason=FLAG_ACCELERATED_BLOCK`

**4. Hipótese**
Segundo a mensagem da coluna acima, existe uma flag a respeito de “parcelas aceleradas”. No meu entendimento, se o cliente possui parcela acelerada, ele não consegue renegociar.

**5. Retry/reprocesso**
> **IMPORTANTE:** Por não saber a política sobre Renegociação e Parcelas Aceleradas, talvez seja necessário escalar para o setor adequado.
Porém vou considerar que posso liberar via função do sistema (uma tentativa).

**6. Correção manual**
> **IMPORTANTE:** Vide acima, vou considerar que não há regra específica para essa situação. Precisaria de mais informações para atuar corretamente.
Porém considerando que eu possa liberar via função do sistema, tentaria utilizar a função de liberação; caso ocasionasse algum erro ou bloqueio, iria escalar para o setor adequado.

**7. Escalação**
Por não ter certeza da política sobre Renegociação e Parcelas Aceleradas, seria necessário escalar para o setor adequado. Entendo que se o cliente quer renegociar a dívida é vantagem para a empresa validar; se está acusando algum bloqueio pelo `FLAG_ACCELERATED_BLOCK`, precisamos entender o motivo. Embora a instrução (FLUXO-IT-SUPPORT.md) indique para não escalar geração de boleto, esse caso possui uma mensagem específica de erro além do conflito entre o que o CX vê e o que o sistema indica.

**8. Comunicação**
`@agente.cx03` O cliente possui parcelas aceleradas, ao menos é isso que o sistema está reportando. Estou escalando o ticket para o setor adequado para verificar o erro, checar se está correto e/ou encontrar uma solução.

**9. Status final**
ESCALADO

---

## IT-1006 — Exclusão de cadastro/Dados - 901.666.666-06

**1. Onde consulto primeiro**
Sistema interno → contrato - / CPF 901.666.666-06 → coluna 'lgpd_delete_requested'

**2. O que busco**
Checar se já existe a solicitação para exclusão dos dados de cadastro.

**3. O que encontrei**
CSV: `lgpd_delete_requested=sim`

**4. Hipótese**
Identifiquei no sistema que já existe a informação de solicitação da remoção do cadastro.

**5. Retry/reprocesso**
Utilizarei a ferramenta para exclusão/desativação ou escalarei para o setor adequado. Não há pendências.

**6. Correção manual**
Utilizarei a ferramenta para exclusão/desativação ou escalarei para o setor adequado. Não há pendências. A solicitação do cliente é válida.

**7. Escalação**
N/A. Escala (para o DEV) apenas se a exclusão/desativação acusar erro.

**8. Comunicação**
`@agente.cx05` Já existia a solicitação para a exclusão/desativação do cadastro do cliente. Estou efetuando os procedimentos.

**9. Status final**
SOLUCIONADO

---

## IT-1007 — Erro ou demora na emissão de Termo de quitação – 901.777.777-07

**1. Onde consulto primeiro**
Sistema interno → contrato 80007001 / CPF 901.777.777-07 → colunas 'contract_status', 'term_email_status'

**2. O que busco**
Confirmar se o contrato já está quitado.

**3. O que encontrei**
CSV: `contract_status=quitado`, `term_email_status=falha_envio`

**4. Hipótese**
O Contrato está quitado, porém ocorreu erro no disparo do termo de quitação.

**5. Retry/reprocesso**
Efetuaria o disparo do Termo de Quitação pelo sistema.

**6. Correção manual**
Após efetuar o disparo, iria acompanhar o status; é possível que o e-mail esteja incorreto ou a caixa de entrada cheia. Em caso de algum desses erros, solicitaria ao agente obter um novo endereço de e-mail.

**7. Escalação**
N/A. Escala (DEV) apenas se o disparo indicar algum erro de sistema.

**8. Comunicação**
`@agente.cx06` Efetuei novo disparo do e-mail com o termo para o e-mail `cliente_ficticio_g@cliente.com.br`. Solicitar o cliente a confirmação do recebimento.

**9. Status final**
SOLUCIONADO

---

## IT-1008 — Desembolso ao cliente - 901.888.888-08

**1. Onde consulto primeiro**
Sistema interno → contrato 80008001 / CPF 901.888.888-08 → colunas 'disbursement_status', 'contract_status'

**2. O que busco**
Confirmar o status do desembolso e identificar o motivo do status.

**3. O que encontrei**
CSV: `disbursement_status=indeferido`, `disbursement_error=BANCARIZADOR_OK`, `contract_status=cancelado`

**4. Hipótese**
O cliente já possui outra solicitação/contrato ativo: 80008002, solicitada em 10/06/2026. Talvez relação com o ticket IT-1011?

**5. Retry/reprocesso**
N/A. O contrato indicado está cancelado.

**6. Correção manual**
N/A. No meu entendimento, só pode existir 1 contrato ativo por CPF/Cliente e por isso o BANCARIZADOR cancelou o mesmo. Ao meu ver não é possível solicitar um novo contrato enquanto o dispositivo já está sendo utilizado como garantia em outro contrato.

**7. Escalação**
N/A. Precisa apenas alertar ao cliente sobre dupla contratação.

**8. Comunicação**
`@agente.cx07` Identifiquei que esse cliente já possui um outro contrato ativo (80008002). O Contrato mencionado (80008001) já consta como cancelado. Solicitar ao cliente aguardar o desembolso do contrato 80008002.

**9. Status final**
SOLUCIONADO

---

## IT-1009 — Desembolso ao cliente - 901.999.999-09

**1. Onde consulto primeiro**
Sistema interno → contrato 80009001 / CPF 901.999.999-09 → colunas 'contract_status', 'disbursement_status', 'disbursement_error'

**2. O que busco**
Identificar qual é o erro que está acusando nessas tentativas.

**3. O que encontrei**
CSV: `contract_status=assinatura_pendente`, `disbursement_status=aguardando_desembolso`, `disbursement_error=SYNC_STATUS_MISMATCH`

**4. Hipótese**
Falta a assinatura do contrato.

**5. Retry/reprocesso**
Embora o erro seja acusado no desembolso (`SYNC_STATUS_MISMATCH`), ele é devido ao status do contrato não estar ativo ou assinado. Efetuaria nova verificação do status do contrato.

**6. Correção manual**
Caso eu possua acesso aos dados do contrato, confirmar se o mesmo foi assinado. Se estiver tudo OK, fazer o update do `contract_status` para 'assinado'. Caso o contrato não esteja assinado, devolver ao Agente pedindo a assinatura do mesmo.

**7. Escalação**
N/A. Apenas se acusar erro no update descrito acima.

**8. Comunicação**
`@agente.cx08`  
* **Possibilidade 1:** Identifiquei que o contrato está assinado corretamente e o sistema não foi atualizado. Fiz a atualização do mesmo no sistema e já executei o desembolso;  
* **Possibilidade 2:** O cliente não possui o contrato devidamente assinado, por isso está acusando erro. Peça para o mesmo confirmar a assinatura e logo após o desembolso será efetuado.

**9. Status final**
SOLUCIONADO

---

## IT-1010 — Erro ao gerar parcela(s) cobrança (sistema interno) – 901.101.010-10

**1. Onde consulto primeiro**
Sistema interno → contrato 80010001 / CPF 901.101.010-10 → colunas 'installment_ref

**2. O que busco**
Confirmar qual foi o último boleto gerado.

**3. O que encontrei**
CSV: `installment_ref=parcela_10;parcela_11,payment_id=doc_acc_fict_10`

**4. Hipótese**
Cliente gerou parcela acelerada e quer pagar apenas a próxima (10).

**5. Retry/reprocesso**
N/A. Apenas efetuar o acerto solicitado.

**6. Correção manual**
Considerando que eu tenho autorização para o procedimento e esteja de acordo com a política da empresa, efetuaria o cancelamento da parcela acelerada (10+11) `doc_acc_fict_10` e regeraria a cobrança da parcela 10.

**7. Escalação**
N/A. Apenas se acusar erro no procedimento descrito.

**8. Comunicação**
`@agente.cx09` Efetuei o cancelamento da parcela acelerada 11 e com isso regerei a cobrança da parcela 10.

**9. Status final**
SOLUCIONADO

---

## IT-1011 — Desembolso ao cliente – 901.888.888-08

**1. Onde consulto primeiro**
Sistema interno → contrato 80008002 / CPF 901.888.888-08 → colunas 'contract_status', 'disbursement_status'

**2. O que busco**
Confirmar o status do contrato e do desembolso.

**3. O que encontrei**
CSV: `contract_status=assinado`, `disbursement_status=aguardando_desembolso`

**4. Hipótese**
Existe algum erro que não é compartilhado com minha base de dados? Há algum problema na no BANCARIZADOR?

**5. Retry/reprocesso**
Faria nova tentativa de desembolso, pois a minha base de dados consta apenas a tentativa do dia 10/06 às 22:00.

**6. Correção manual**
Tentativa de desembolso manualmente, verificar se existe algum outro status reportado. Em caso negativo, precisaria escalar, pois minha base de dados não traz mais nenhuma informação (será que ocorreu algum conflito devido ao ticket IT-1008?).

**7. Escalação**
BANCARIZADOR. Considerando que minha tentativa não retornou sucesso, escalaria para o time adequado para verificar o motivo de ainda aguardar o desembolso. Reportaria que esse cliente tentou criar um novo contrato (ticket IT-1008 / CB 80008001) no dia seguinte e que talvez essa situação tenha gerado alguma inconsistência na transação.

**8. Comunicação**
`@agente.cx07` Conforme reportado no ticket IT-1008, esse cliente gerou 2 contratos em menos de 24 horas e provavelmente isso tenha gerado algum flag de alerta no BANCARIZADOR. Já escalei para eles e aguardo retorno. Assim que possível envio a análise final e solução.

**9. Status final**
ESCALADO

---

## IT-1012 — Dúvida — prazo de desembolso - 901.121.212-12

**1. Onde consulto primeiro**
Sistema interno → contrato 80012001 / CPF 901.121.212-12 → colunas 'contract_status', 'disbursement_status'

**2. O que busco**
Confirmar o status do contrato e do desembolso.

**3. O que encontrei**
CSV: `contract_status=assinado`, `disbursement_status=aguardando_desembolso`

**4. Hipótese**
Existe algum erro que não é compartilhado com minha base de dados ou ainda está dentro do prazo.

**5. Retry/reprocesso**
Faria nova tentativa de desembolso, pois a minha base de dados consta apenas a tentativa do dia 11/06 às 11:30.

**6. Correção manual**
Tentativa de desembolso manualmente, verificar se existe algum outro status reportado. Em caso negativo, precisaria escalar, pois minha base de dados não traz mais nenhuma informação.  
> **IMPORTANTE:** Não tenho a informação do prazo mínimo/máximo para liberação.

**7. Escalação**
BANCARIZADOR. Considerando que minha tentativa não retornou sucesso, escalaria para o time adequado para verificar o motivo de ainda aguardar o desembolso – caso a liberação tivesse que ser imediata. Talvez exista algum problema na liberação, pois o ticket anterior (IT-1011) tem erro semelhante.

**8. Comunicação**
`@agente.cx10` Já estou verificando com o BANCARIZADOR a respeito do prazo e tentativas. Aguardar retorno final.  
> **IMPORTANTE:** Caso o prazo seja em até 24h, por exemplo, apenas repassaria essa informação ao Agente.

**9. Status final**
ESCALADO
