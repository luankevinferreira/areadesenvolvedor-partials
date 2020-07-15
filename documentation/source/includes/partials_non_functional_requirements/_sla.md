## Nível de Serviço (SLA)
O suporte eficaz da disponibilidade do Open Banking mantém níveis consistentes de serviços do sistema. 
Níveis de Serviço definidos:  

•	Cada endpoint da API deve estar disponível 95% do tempo durante cada período de 24 horas.  
•	Cada endpoint da API deve estar disponível 99.5% do tempo durante cada período de 3 meses.  
•	Cada endpoint da API deve retornar o primeiro byte de resposta dentro de 1000ms por 95% das chamadas.  
•	O tempo de resposta será medido por um cliente externo com uma latência de rede máxima de 50 ms (tempo para o primeiro byte).  

Informativamente, esse nível de serviço representa aproximadamente um downtime máximo de 0,5% por trimestre, ou:  

•	18s por hora  
•	7,2min (432s) por dia  
•	3,6h (216m) por mês  
•	10,8h (648m) por trimestre  

A definição de um período de indisponibilidade é qualquer período de tempo em que qualquer um dos endpoints do API definidos na norma é incapaz de fornecer uma resposta confiável a uma solicitação construída de forma apropriada. 

### Checagem de disponibilidade: 

    GET channels/v1/status  
    GET product-services/v1/status  

 - Cada 30 segundos, a API de status é chamada com timeout de 1s.
	 - Se a chamada receber um OK: não contar downtime. 
	 - Se a chamada receber um SCHEDULED_OUTAGE: 
		 - Se a chamada for feita entre 23h e 05h, o contador de SCHEDULED_OUTAGE é iniciado com 30 segundos acrescidos. 
		 - Cada nova chamada vai adicionando 30 segundos mais ao contador de SCHEDULED_OUTAGE, até que uma chamada volte outro valor ou a chamada for feita depois das 05h.
	 - Se a chamada receber um:
		 - PARTIAL_FAILURE ou
		 - UNAVAILABLE, ou 
		 - SCHEDULED_OUTAGE fora do período de 23h e 05h, 
		 - ou o serviço não responder a chamada:  
		    - O contador de downtime é iniciado com 30 segundos acrescidos.  
			- Cada nova chamada vai adicionando 30 segundos a mais ao contador de downtime, até que uma chamada volte OK.  

 ![Figura 1: Fluxo control downtime](/images/extras/fluxo_checagem_disponibilidade.png)

O downtime deve ser calculado como o número total de segundos simultâneos por chamada da API, por período de 24 horas, começando e terminando à meia-noite, que qualquer endpoint da API não esteja disponível, dividido por 86.400 (total de segundos em 24 horas) e expresso como uma porcentagem.

**A disponibilidade é calculada como 100% - % de Indisponibilidade.**

Não conta como downtime:

 - Uma indisponibilidade por mês, por 3h entre 23h e 05h, desde que reportado com uma semana de antecedência ao diretório.
 - Indisponibilidade para correção emergencial, desde que aprovado pelo diretório.
