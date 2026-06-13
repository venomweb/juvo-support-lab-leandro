Prioridade no atendimento dos tickets:


**1. IT-1009**  
Novo cliente. Priorizar para concluir a contratação e liberação do desembolso. Checar a assinatura do contrato. Resolve no Suporte ou CX.

**2. IT-1002**  
Potencial novo cliente. Verificar dados do contrato/conta informada. Possibilidade de escalar para o BANCARIZADOR caso os dados estejam corretos.

**3. IT-1005**  
Erro na tentativa de renegociação. Embora o CX indique que não há parcelas aceleradas pude confirmar na base de dados que existe sim. Talvez necessário escalar para outro setor para explicar o motivo de não permitir a renegociação quando há parcelas aceleradas.

**4. IT-1011**  
Falta apenas efetuar o desembolso. Possibilidade de escalar para o BANCARIZADOR, priorizaria esse antes dos itens abaixo (5 / 6) pois claramente o cliente está ansioso para receber os valores (vide ticket IT-1008).

**5. IT-1001**  
Falta apenas efetuar o desembolso. Possibilidade de escalar para o BANCARIZADOR.

**6. IT-1012**  
Falta apenas efetuar o desembolso. Possibilidade de escalar para o BANCARIZADOR caso o prazo de desembolso esteja vencido. Caso contrário avisar ao CX o prazo e pedir para o cliente aguardar.

**7. IT-1004**  
Regerar novo boleto contendo apenas a parcela 6, ao invés do boleto de quitação. Em análise para identificar os procedimento e regras de negócio.

**8. IT-1010**  
Regerar novo boleto apenas com a parcela 10, cliente havia previamente gerado parcela acelerada 10+11. Solucionado no Suporte.

**9. IT-1003**  
PIX compensado no Banco, ainda consta como aberto. Suporte efetuará o update manual e CX irá reportar ao cliente.

**10. IT-1008**  
Contrato cancelado. Verificar ticket IT-1011. Cliente tentou gerar um novo contrato enquanto aguardava o desembolso do contrato 80008002. Reportar ao cliente para aguardar o desembolso do contrato 80008001.

**11. IT-1007**  
Cliente já finalizou os pagamentos e precisa apenas receber o Termo de Quitação. Baixa prioridade. Solucionado no Suporte.

**12. IT-1006**  
Solicitação de exclusão do cadastro, baixa prioridade. Caso seja uma função do Suporte o mesmo a fará, caso contrário irá escalar para a equipe adequada.




**Considerações finais**  
Eu acredito que tenha encontrado soluções lógicas para todos os casos, não tive nenhuma dificuldade em minha análise. A única barreira foi entender o que eu tenho acesso e o que posso executar.   
De modo geral eu priorizo novos clientes para finalizar o contrato, pois perder um cliente - por demora no atendimento - é algo que raramente conseguimos recuperar. Após isso deixei a renegociação pois precisamos aproveitar o interesse do cliente em resolver as pendências, após isso veioa liberação do desembolso. A geração de novos boletos vem logo atrás, mas não vejo como urgência (dependendo da regra da JUVO é possível que coloque gerar boletos antes da liberação do desembolso) pois é mais importante fidelizar um novo cliente (desembolso). Por fim os demais itens.   
Em alguns tickets eu precisei colocar um "OU"/"CASO"/etc pois precisaria entender melhor a estrutura e qual equipe/setor é o mais adequado para ESCALAR. Exemplo, problema na renegociação: Foi um bloqueio do sistema, foi erro do sistema em bloquear? E caso seja um problema, devo escalar pro DEV ou para o time de PRODUTO para entender o motivo do erro?  

No mais, estou feliz com minhas respostas.
