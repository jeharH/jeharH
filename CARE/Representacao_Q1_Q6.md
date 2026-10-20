# CARE — Representação das respostas

Não há instância de dados fornecida: as respostas indicam como recuperar os resultados.

## Q1 · Exames não requeridos nos últimos 30 dias

**Caminho:** LABORATORIO → cobre → TIPO_EXAME ← é requerido em ← SOLICITACAO_EXAME

Para o laboratório L, selecionar os tipos cobertos para os quais não existe solicitação entre agora − 30 dias e agora. O filtro temporal usa dataHoraSolicitacao, não a data agendada. Consideram-se pedidos na holding, mesmo ainda não agendados.

## Q2 · Primeiras consultas e retornos de X

**Caminho:** PACIENTE → recebe → ATENDIMENTO → CONSULTA

Selecionar o paciente X por idPaciente e listar as consultas, agrupadas por tipoConsulta ∈ {primeira, retorno}, com a dataHoraAtendimento herdada.

## Q3 · Solicitações de internação não agendadas

**Caminho:** CONSULTA → origina → SOLICITACAO_INTERNACAO → é agendada como → INTERNACAO

Selecionar solicitações sem ocorrência associada de INTERNACAO. A participação (0,1) permite guardar o pedido antes do agendamento.

## Q4 · Turnos de A na ala Z e substitutos

**Caminho:** ENFERMEIRO ⊆ FUNCIONARIO → é titular de → PERIODO_TRABALHO ← recebe ← ALA; PERIODO → registra → SUBSTITUICAO ← substitui em ← ENFERMEIRO

Filtrar o titular A e a ala Z; listar inicioPrevisto e fimPrevisto. Para cada período, apresentar substituições, intervalos efetivos e o enfermeiro substituto. O titular continua preservado, inclusive quando o substituto vem de outro hospital.

## Q5 · Salas cirúrgicas no 6º ou 9º andar

**Caminho:** HOSPITAL → possui → ALA → ALA_CIRURGIA → contém → SALA_OPERACAO

Filtrar andar de ALA_CIRURGIA em {6,9}; listar suas salas e hospitais. O andar pertence à ala porque todas as suas salas ocupam um único andar.

## Q6 · Ocupantes do leito L em março/2026

**Caminho:** LEITO → é designado a → INTERNACAO ⊆ ATENDIMENTO ← recebe ← PACIENTE

Selecionar codigoLeito = L e ocupações que intersectam [01/03/2026 00:00, 01/04/2026 00:00): inicioOcupacao < 01/04 e (fimOcupacao ausente ou fimOcupacao > 01/03). Excluir internações sem início efetivo e listar os pacientes.

## Premissas e restrições

1. ALA: especialização total e disjunta é hipótese. FUNCIONARIO: parcial e sobreposta; não se impõe exclusão entre médico e enfermeiro sem regra explícita. Os identificadores dos subtipos são herdados.

2. ATENDIMENTO generaliza consulta, internação e cirurgia para registrar diagnóstico composto ao final de qualquer atendimento. Data efetiva e diagnóstico são obrigatórios na conclusão; podem faltar no agendamento. Médico = responsável, sem excluir equipes.

3. Substituição: titular do período deve ser enfermeiro e diferente do substituto; intervalo efetivo deve estar dentro do período. Substituições do mesmo período não se sobrepõem. Hospital de origem não muda. Registro CRM usa número + UF como chave profissional alternativa.

4. Internação: pedido nasce de consulta de retorno; paciente e médico solicitante são recuperados pela consulta. O paciente internado deve ser o mesmo do pedido. Uma internação designa um leito, sem transferências nesta hipótese. Fim > início; ocupações do mesmo leito não se sobrepõem. Fim ausente indica ocupação em curso.

5. Exame: laboratório do agendamento deve cobrir o tipo pedido e ter contrato vigente. Resultado exige exame realizado; cada pedido tem no máximo um resultado. SUS ou convênio deve ser informado, sem impor exclusividade. Endereços são simples porque seus componentes não foram exigidos.
