# 📦 Relatório Final

> **Atividade:** Bug Report Profissional + Code Review Guiado
> **Curso:** Qualidade de Software
> **Professor:** Prof. Claudio Nunes

---

## 👥 Identificação da dupla

| Nome completo | RA | GitHub |
|---|---|---|
| Gabriela Carrodeguas| [229026] | [@GabiCarrodeguas] |
| Miguel Belleza| [227500] | [@Maikom118] |

**Ambiente de testes:** Chrome no Windows 11, aplicação executada via GitHub Pages do fork, edição realizada no editor web do GitHub.

---

## 📋 Sumário

- [Parte A — Bug Reports](#parte-a--bug-reports)
- [Parte B — Code Review](#parte-b--code-review)
- [Reflexão final](#-reflexão-final)
- [Declarações](#-declarações)

---

## Parte A — Bug Reports

BUG-001  
Título: Permite criar tarefa com campos obrigatórios vazios  

Severidade: Alta  
Justificativa da severidade: Permite criação de dados inválidos, comprometendo a integridade das informações.  

Prioridade: P1  
Justificativa da prioridade: Impacta diretamente a funcionalidade principal da aplicação.  

Ambiente:  
Navegador: Chrome  
Sistema Operacional: Windows 11  
Versão da aplicação: TarefaQS v1.0.0  

Passos para reprodução:  
1. Acessar a aplicação  
2. Não preencher nenhum campo  
3. Clicar em "Adicionar tarefa"  

Resultado esperado:  
O sistema deveria impedir o envio e exibir erro.  

Resultado obtido:  
A tarefa é criada com dados vazios.  

Evidência:  
Tarefa exibida sem título.  

Sugestão de causa raiz (opcional):  
Ausência de validação no formulário.  

---

BUG-002  
Título: Campo de prioridade aceita valores inválidos  

Severidade: Média  
Justificativa da severidade: Gera inconsistência nos dados e afeta ordenação.  

Prioridade: P2  
Justificativa da prioridade: Impacta a experiência do usuário.  

Ambiente:  
Navegador: Chrome  
Sistema Operacional: Windows 11  
Versão da aplicação: TarefaQS v1.0.0  

Passos para reprodução:  
1. Criar tarefa  
2. Inserir prioridade 0, 99 ou decimal  
3. Enviar  

Resultado esperado:  
Aceitar apenas valores de 1 a 5.  

Resultado obtido:  
Qualquer valor é aceito.  

Evidência:  
Prioridade exibida fora do padrão.  

Sugestão de causa raiz (opcional):  
Falta de validação e uso de step="any".  

---

BUG-003  
Título: Contador de tarefas pendentes exibe valor incorreto  

Severidade: Média  
Justificativa da severidade: Apresenta dados incorretos ao usuário.  

Prioridade: P2  
Justificativa da prioridade: Impacta a tomada de decisão.  

Ambiente:  
Navegador: Chrome  
Sistema Operacional: Windows 11  
Versão da aplicação: TarefaQS v1.0.0  

Passos para reprodução:  
1. Criar tarefa com prioridade 5  
2. Não concluir  
3. Observar contador  

Resultado esperado:  
Contagem correta de tarefas pendentes.  

Resultado obtido:  
Valor duplicado.  

Evidência:  
Número maior que o total real.  

Sugestão de causa raiz (opcional):  
Soma duplicada de tarefas urgentes.  

---

## Parte B — Code Review

### Resumo

| # | Linha | Dimensão | Rótulo | Severidade |
|---|-------|----------|--------|------------|
| 1 | ~10 | Segurança | blocker | Crítica |
| 2 | ~2 | Padrões | nit | Baixa |
| 3 | ~15 | Erros | major | Média |
| 4 | ~30 | Erros | major | Média |
| 5 | ~40 | Complexidade | major | Alta |
| 6 | ~120 | Complexidade | major | Média |


### Findings detalhadas

Finding 1
Linha(s): ~10–12
Rótulo: blocker
Dimensão: Segurança
Severidade: Crítica
Problema:
Possível vulnerabilidade de SQL Injection ao concatenar diretamente o input do usuário na query.
const query = "SELECT * FROM usuarios WHERE nome = '" + nome + "'";
Sugestão de correção:
Utilizar queries parametrizadas (prepared statements).
const query = "SELECT * FROM usuarios WHERE nome = ?";
return db.executarQuery(query, [nome]);
________________________________________
Finding 2
Linha(s): ~1–5
Rótulo: nit
Dimensão: Padrões
Severidade: Baixa
Problema:
A constante TIPOS_VALIDOS é declarada, mas nunca utilizada para validação.
Sugestão de correção:
Validar o tipo do usuário antes de cadastrar.
if (!TIPOS_VALIDOS.includes(dados.tipo)) {
  throw new Error('Tipo de usuário inválido');
}
________________________________________
Finding 3
 Linha(s): ~15–25
Rótulo: major
Dimensão: Erros
Severidade: Média
Problema:
Falta de tratamento de erro em operações assíncronas (await).
Sugestão de correção:
Utilizar try/catch.
try {
  const senhaHash = await crypto.hash(dados.senha, salt);
} catch (erro) {
  logger.error('Erro ao gerar hash', erro);
  throw erro;
}
________________________________________
Finding 4
 Linha(s): ~30–35
Rótulo: major
Dimensão: Erros
Severidade: Média
Problema:
Possível erro ao acessar usuário inexistente em atualizarEmail.
const u = await db.buscarPorId('usuarios', id);
u.email = novoEmail;
Sugestão de correção:
Validar se o usuário existe.
if (!u) {
  throw new Error('Usuário não encontrado');
}
________________________________________
Finding 5
Linha(s): ~40–120
Rótulo: major
Dimensão: Complexidade
Severidade: Alta
 Problema:
Função calcularLimiteEmprestimo possui alta complexidade ciclomática (muitos ifs aninhados).
Sugestão de correção:
Refatorar em funções menores ou usar estrutura de regras.
function calcularLimiteProfessor(usuario) { ... }
function calcularLimiteAluno(usuario) { ... }
________________________________________
Finding 6
Linha(s): ~120–170
Rótulo: major
Dimensão: Complexidade
Severidade: Média
 Problema:
Código duplicado entre calcularLimiteEmprestimo e calcularLimiteComSuspensao.
 Sugestão de correção:
Extrair lógica comum para função reutilizável.
function calcularBaseLimite(usuario) { ... }
________________________________________
Finding 7
Linha(s): ~28
Rótulo: nit
Dimensão: Legibilidade
⚠ Severidade: Baixa
Problema:
Uso de nome de variável pouco descritivo (u).
const u = await db.buscarPorId(...)
Sugestão de correção:
Usar nomes mais claros.
const usuario = await db.buscarPorId(...)
________________________________________
Finding 8
Linha(s): ~18–22
Rótulo: major
Dimensão: Segurança
⚠Severidade: Média
 Problema:
Dados sensíveis (email) sendo logados diretamente.
logger.info('Usuario cadastrado: ' + dados.email);
 Sugestão de correção:
Evitar log de dados sensíveis ou mascarar.
logger.info('Usuario cadastrado com sucesso');



---

## 💭 Reflexão final


**Qual dimensão do checklist foi mais difícil aplicar? Por quê?**
A parte de complexidade, pois exige uma análise mais aprofundada da lógica do código e da quantidade de caminhos possíveis dentro das funções. Identificar quando um código se torna complexo demais não é tão evidente quanto encontrar erros simples ou problemas de segurança.

**O que vocês fariam diferente se revisassem o código novamente?**
Dedicaríamos mais tempo à organização das regras de negócio e à identificação de padrões de refatoração. Também poderíamos discutir melhor entre a dupla para validar se determinados pontos são realmente defeitos ou apenas escolhas de implementação.

---

## 📣 Declarações


### Uso de IA como parceiro de trabalho

- [ ] Não usamos IA nesta atividade.
- [X] Usamos IA para esclarecer conceitos teóricos.
- [X] Usamos IA para revisar a redação dos bug reports.
- [ ] Usamos IA para discutir se um achado era ou não um defeito.
- [X] Uso específico: apoio para seguir a estruturação pedida pelo professor dos relatórios e identificação de possíveis melhorias no código

### Declaração de autoria

Declaramos que este relatório é de autoria da dupla, que exploramos
pessoalmente a aplicação da Parte A e lemos o código da Parte B. As
findings aqui registradas representam nosso próprio julgamento
técnico.
