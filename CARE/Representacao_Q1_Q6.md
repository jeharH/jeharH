# CARE — Representação das respostas (Etapa 3)

Como não foram fornecidos registros de exemplo, as respostas explicam como encontrar as informações pelo modelo, seguindo o formato do entregável.

| Pergunta | Caminho percorrido no modelo | Resposta obtida |
|---|---|---|
| **Q1 — Quais exames cobertos pelo laboratório L não foram requeridos nos últimos 30 dias?** | LABORATORIO → cobre → TIPO_EXAME → é requerido em → SOLICITACAO_EXAME | Primeiro, identifico os tipos de exame cobertos pelo laboratório L. Depois, verifico as solicitações pela **dataHoraSolicitacao** e seleciono os tipos que não possuem nenhuma solicitação nos últimos 30 dias. Assim, encontro os exames oferecidos pelo laboratório que não foram requeridos nesse período, incluindo aqueles que nunca foram solicitados. |
| **Q2 — Quais são as primeiras consultas e as consultas de retorno de certo paciente X?** | PACIENTE → recebe → ATENDIMENTO → CONSULTA | Localizo o paciente X pelo **idPaciente** e identifico seus atendimentos classificados como consulta. Em seguida, separo as consultas pelo atributo **tipoConsulta**, que indica se são primeiras consultas ou retornos, e apresento a data e a hora de cada atendimento. |
| **Q3 — Quais solicitações de internação ainda não foram agendadas?** | SOLICITACAO_INTERNACAO → é agendada como → INTERNACAO | Verifico quais solicitações de internação ainda não estão associadas a uma ocorrência de **INTERNACAO**. Como essa associação é opcional para a solicitação, o modelo permite registrar o pedido antes de existir um agendamento. Portanto, as solicitações sem internação associada são aquelas que ainda aguardam agendamento. |
| **Q4 — Quais foram os dias e períodos de trabalho do enfermeiro A na ala Z? Ele foi substituído? Por quem?** | ENFERMEIRO ⊆ FUNCIONARIO → é titular de → PERIODO_TRABALHO ← recebe ← ALA; PERIODO_TRABALHO → registra → SUBSTITUICAO ← substitui em ← ENFERMEIRO | Identifico os períodos em que o enfermeiro A era o titular da escala na ala Z, utilizando **inicioPrevisto** e **fimPrevisto** para obter os dias e horários. Depois, verifico se existem substituições vinculadas a esses períodos. Quando houver, apresento o enfermeiro substituto e o intervalo efetivo da substituição. O titular original permanece registrado, mesmo quando o substituto pertence a outro hospital. |
| **Q5 — Existem salas de cirurgia no 6º ou no 9º andar?** | HOSPITAL → possui → ALA → ALA_CIRURGIA → contém → SALA_OPERACAO | Consulto o atributo **andar** das alas de cirurgia e seleciono aquelas localizadas no 6º ou no 9º andar. Depois, identifico as salas de operação pertencentes a essas alas. Se houver salas associadas, a resposta será positiva, sendo possível apresentar seus identificadores e os respectivos hospitais. |
| **Q6 — Quais pacientes ocuparam o leito L durante março de 2026?** | LEITO → é designado a → INTERNACAO ⊆ ATENDIMENTO ← recebe ← PACIENTE | Localizo o leito L pelo **codigoLeito** e verifico as internações com ocupação efetiva durante março de 2026. Seleciono aquelas cujo início ocorreu antes de **01/04/2026** e cujo término ocorreu depois de **01/03/2026**, incluindo ocupações ainda em andamento. Assim, também considero pacientes que entraram antes de março e permaneceram no leito durante parte do mês. Por fim, identifico os pacientes associados a essas internações, desconsiderando agendamentos que não chegaram a iniciar a ocupação. |

## Premissas e restrições

1. ALA: especialização total e disjunta é hipótese. FUNCIONARIO: parcial e sobreposta; não se impõe exclusão entre médico e enfermeiro sem regra explícita. Os identificadores dos subtipos são herdados.

2. ATENDIMENTO generaliza consulta, internação e cirurgia para registrar diagnóstico composto ao final de qualquer atendimento. Data efetiva e diagnóstico são obrigatórios na conclusão; podem faltar no agendamento. Médico = responsável, sem excluir equipes.

3. Substituição: titular do período deve ser enfermeiro e diferente do substituto; intervalo efetivo deve estar dentro do período. Substituições do mesmo período não se sobrepõem. Hospital de origem não muda. Registro CRM usa número + UF como chave profissional alternativa.

4. Internação: pedido nasce de consulta de retorno; paciente e médico solicitante são recuperados pela consulta. O paciente internado deve ser o mesmo do pedido. Uma internação designa um leito, sem transferências nesta hipótese. Fim > início; ocupações do mesmo leito não se sobrepõem. Fim ausente indica ocupação em curso.

5. Exame: laboratório do agendamento deve cobrir o tipo pedido e ter contrato vigente. Resultado exige exame realizado; cada pedido tem no máximo um resultado. SUS ou convênio deve ser informado, sem impor exclusividade. Endereços são simples porque seus componentes não foram exigidos.
