# Criar os arquivos do projeto e empacotar em um arquivo ZIP para download
import os
import zipfile

# Definir o diretório onde os arquivos serão criados
project_dir = "/mnt/data/todo-list-profissional"

# Criar diretório do projeto
os.makedirs(project_dir, exist_ok=True)

# Conteúdo dos arquivos
index_html = """<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Lista de Tarefas Profissional</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
  <div class="container">
    <h1>Minha Lista de Tarefas</h1>
    <form id="taskForm">
      <input type="text" id="taskInput" placeholder="Descrição da tarefa" required>
      <input type="text" id="responsibleInput" placeholder="Responsável" required>
      <input type="date" id="dateInput" required>
      <select id="priorityInput" required>
        <option value="Alta">Alta</option>
        <option value="Média">Média</option>
        <option value="Baixa">Baixa</option>
      </select>
      <button type="submit">Adicionar</button>
    </form>
    <ul id="taskList"></ul>
    <button onclick="downloadTasks()">Baixar Lista</button>
  </div>
  <script src="script.js"></script>
</body>
</html>
"""

style_css = """body {
  font-family: Arial, sans-serif;
  background-color: #e0f2ff;
  color: #003366;
  display: flex;
  justify-content: center;
  padding: 20px;
}

.container {
  background-color: #ffffff;
  padding: 20px;
  border-radius: 12px;
  width: 100%;
  max-width: 600px;
  box-shadow: 0 0 10px rgba(0,0,0,0.1);
}

h1 {
  text-align: center;
  color: #005ea8;
}

form input, form select, form button {
  margin: 5px 0;
  padding: 10px;
  width: 100%;
  box-sizing: border-box;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  background-color: #f1f9ff;
  margin: 10px 0;
  padding: 10px;
  border-left: 5px solid #005ea8;
  border-radius: 5px;
}

button {
  background-color: #005ea8;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

button:hover {
  background-color: #004b8c;
}
"""

script_js = """const form = document.getElementById("taskForm");
const list = document.getElementById("taskList");

form.addEventListener("submit", function (e) {
  e.preventDefault();

  const task = document.getElementById("taskInput").value;
  const responsible = document.getElementById("responsibleInput").value;
  const date = document.getElementById("dateInput").value;
  const priority = document.getElementById("priorityInput").value;

  const li = document.createElement("li");
  li.innerHTML = `
    <strong>${task}</strong><br>
    Responsável: ${responsible} | Data: ${date} | Prioridade: ${priority}<br>
    <label><input type="checkbox"> Respondido</label>
  `;

  li.setAttribute("data-priority", priorityValue(priority));
  list.appendChild(li);
  ordenarLista();

  form.reset();
});

function priorityValue(priority) {
  if (priority === "Alta") return 1;
  if (priority === "Média") return 2;
  return 3;
}

function ordenarLista() {
  const items = Array.from(list.children);
  items.sort((a, b) => a.getAttribute("data-priority") - b.getAttribute("data-priority"));
  items.forEach(item => list.appendChild(item));
}

function downloadTasks() {
  let text = "Tarefas:\\n\\n";
  const items = list.querySelectorAll("li");

  items.forEach((item, index) => {
    text += \`\${index + 1}. \${item.textContent.trim()}\\n\\n\`;
  });

  const blob = new Blob([text], { type: "text/plain" });
  const url = URL.createObjectURL(blob);

  const a = document.createElement("a");
  a.href = url;
  a.download = "lista-de-tarefas.txt";
  a.click();

  URL.revokeObjectURL(url);
}
"""

# Criar os arquivos
with open(os.path.join(project_dir, "index.html"), "w", encoding="utf-8") as f:
    f.write(index_html)

with open(os.path.join(project_dir, "style.css"), "w", encoding="utf-8") as f:
    f.write(style_css)

with open(os.path.join(project_dir, "script.js"), "w", encoding="utf-8") as f:
    f.write(script_js)

# Criar o ZIP
zip_path = "/mnt/data/todo-list-profissional.zip"
with zipfile.ZipFile(zip_path, 'w') as zipf:
    for root, dirs, files in os.walk(project_dir):
        for file in files:
            file_path = os.path.join(root, file)
            zipf.write(file_path, arcname=os.path.relpath(file_path, project_dir))

zip_path
