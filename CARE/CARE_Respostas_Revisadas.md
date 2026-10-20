# CARE — Respostas revisadas com exemplos hipotéticos

Todos os exemplos são fictícios; não representam dados fornecidos no enunciado.

## Q1 — Exames não requeridos nos últimos 30 dias

**Caminho:** LABORATORIO → cobre → TIPO_EXAME ← é requerido em ← SOLICITACAO_EXAME

**Como obtenho a resposta:** Primeiro, identifico os tipos de exame cobertos pelo laboratório L. Depois, consulto dataHoraSolicitacao e seleciono os tipos sem pedidos nos últimos 30 dias na holding. Isso inclui os exames nunca solicitados e considera pedidos ainda não agendados.

**Exemplo hipotético:** Considerando 30/04/2026 às 12h como referência, uso o intervalo de 31/03/2026 às 12h até 30/04/2026 às 12h. O laboratório Vida cobre hemograma, ultrassonografia e eletrocardiograma. O hemograma foi solicitado em 14/04; a última ultrassonografia, em 15/03; e não há pedidos de eletrocardiograma.

**Resposta concreta:** Ultrassonografia e eletrocardiograma não foram requeridos nos últimos 30 dias.

## Q2 — Primeiras consultas e retornos do paciente X

**Caminho:** PACIENTE → recebe → ATENDIMENTO → CONSULTA

**Como obtenho a resposta:** Localizo X por idPaciente e identifico seus atendimentos do tipo CONSULTA. Em seguida, separo as ocorrências por tipoConsulta e apresento dataHoraAtendimento, herdada de ATENDIMENTO.

**Exemplo hipotético:** A paciente Maria Souza, idPaciente P001, teve duas consultas registradas: C010 em 02/04/2026 às 9h, classificada como primeira consulta; e C018 em 16/04/2026 às 10h, classificada como retorno.

**Resposta concreta:** Primeira consulta: C010, em 02/04/2026 às 9h. Retorno: C018, em 16/04/2026 às 10h.

## Q3 — Solicitações de internação não agendadas

**Caminho:** SOLICITACAO_INTERNACAO → é agendada como → INTERNACAO

**Como obtenho a resposta:** Seleciono as solicitações que não possuem INTERNACAO associada. A participação (0,1) permite registrar o pedido antes do agendamento, sem precisar criar uma internação incompleta.

**Exemplo hipotético:** Existem os pedidos SI101, SI102 e SI103. SI102 já está associado à internação I020, agendada para 18/04/2026 às 8h. SI101 e SI103 não possuem internação associada.

**Resposta concreta:** SI101 e SI103 ainda não foram agendadas.

## Q4 — Períodos do enfermeiro A na ala Z e substitutos

**Caminho:** ENFERMEIRO ⊆ FUNCIONARIO → é titular de → PERIODO_TRABALHO ← recebe ← ALA; PERIODO_TRABALHO → registra → SUBSTITUICAO ← substitui em ← ENFERMEIRO

**Como obtenho a resposta:** Identifico os períodos em que A era o titular da escala na ala Z. Verifico inicioPrevisto e fimPrevisto e, quando houver SUBSTITUICAO, apresento o enfermeiro substituto e seu intervalo efetivo. O titular original continua registrado.

**Exemplo hipotético:** Ana Lima, enfermeira E010 do hospital H1, estava escalada na ala Z em 10/03/2026, das 7h às 13h, e em 11/03/2026, das 13h às 19h. No dia 10, Bruno Reis, E022 do hospital H2, a substituiu das 9h às 11h. Não há substituição no segundo período.

**Resposta concreta:** 10/03, 7h–13h: substituição por Bruno Reis das 9h às 11h; Ana atuou nos demais intervalos. 11/03, 13h–19h: sem substituição registrada.

## Q5 — Salas cirúrgicas no 6º ou no 9º andar

**Caminho:** HOSPITAL → possui → ALA → ALA_CIRURGIA → contém → SALA_OPERACAO

**Como obtenho a resposta:** Seleciono as alas cirúrgicas cujo atributo andar é 6 ou 9. Depois, identifico suas salas de operação e os respectivos hospitais. O andar está na ala porque todas as salas dessa ala ocupam o mesmo andar.

**Exemplo hipotético:** No hospital H1, a ala AC06 fica no 6º andar e contém SO601 e SO602. A ala AC09 fica no 9º andar e contém SO901. A sala SO302 pertence a uma ala do 3º andar.

**Resposta concreta:** Sim. SO601 e SO602 estão no 6º andar, e SO901 está no 9º andar do hospital H1. SO302 não entra na resposta.

## Q6 — Pacientes que ocuparam L em março de 2026

**Caminho:** LEITO → é designado a → INTERNACAO ⊆ ATENDIMENTO ← recebe ← PACIENTE

**Como obtenho a resposta:** Seleciono o leito L e ocupações efetivas que intersectam [01/03/2026 00h, 01/04/2026 00h). O início deve ser anterior a 01/04, e o fim deve ser posterior a 01/03 ou estar ausente. Desconsidero internações sem início efetivo.

**Exemplo hipotético:** No leito L010, Maria ocupou de 27/02 às 14h até 03/03 às 10h; João, de 15/03 às 8h até 20/03 às 16h; e Ana entrou em 31/03 às 18h, permanecendo internada no fechamento de março. Há também um agendamento sem início de ocupação.

**Resposta concreta:** Maria, João e Ana ocuparam L010 em março de 2026. Maria é incluída mesmo tendo entrado em fevereiro. O agendamento sem ocupação efetiva é excluído.

## Registro da revisão

### Estrutura preservada

Não foram acrescentadas entidades, funcionalidades ou novos relacionamentos. Os identificadores, atributos e cardinalidades do modelo anterior foram mantidos. As ampliações mostram o mesmo modelo por assunto; uma caixa lilás é uma referência visual, não outra entidade.

### Atendimento e seus subtipos

Mantida a especialização total e disjunta nos três eventos do cenário. Uma ocorrência de ATENDIMENTO é consulta, internação ou cirurgia; uma cirurgia realizada durante uma internação é outra ocorrência, ligada por “compreende”. Não há especialização de CIRURGIA a partir de INTERNACAO.

### Diagnóstico e datas

Mantido o diagnóstico como atributo composto de ATENDIMENTO: tipoDiagnostico, complicacoes e precaucoes. Isso atende à exigência de diagnóstico ao final de qualquer atendimento. [0,1] permite ausência antes da realização; ao concluir, data/hora efetiva e diagnóstico são obrigatórios. A internação agendada pode existir antes da ocupação.

### Hipóteses que o enunciado não fecha

Foram preservadas, com indicação explícita: alas totais/disjuntas; funcionários parciais/sobrepostos; um médico responsável por atendimento; até uma internação agendada por solicitação; um leito por internação e ausência de transferências. O vínculo de uma cirurgia à internação é opcional porque o texto não determina que toda cirurgia exija internação. Equipes médicas não são detalhadas.

### Integridade dos vínculos e dos intervalos

Paciente da internação deve ser o mesmo do pedido originado na consulta de retorno; cirurgia associada deve atender esse paciente. Em cada leito, ocupações efetivas não se sobrepõem. Início e fim são datetimes e usam intervalo [início,fim). O fim, quando preenchido, deve ser posterior ao início. Substituto é diferente do titular enfermeiro e atua dentro do intervalo da escala.

### Exames, cadastro e identificadores

O laboratório escolhido cobre o tipo pedido e tem contrato vigente na execução. Cada exame realizado gera um único resultado. O cadastro do paciente é único na holding; SUS ou convênio deve ser informado, admitindo ambos. IDs técnicos distinguem eventos e cadastros; subtipos herdam o ID do supertipo. Não se desenham chaves estrangeiras no modelo conceitual.

### Validação das seis questões

Q1 usa data do pedido e ausência de solicitações no intervalo; Q2 usa tipoConsulta; Q3 testa ausência de internação; Q4 preserva titular e substituto por intervalo; Q5 usa o andar da ala cirúrgica; Q6 verifica interseção de ocupações efetivas. Os exemplos das páginas anteriores produzem respostas concretas por esses caminhos.
