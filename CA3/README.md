# 🎓 Escola Municipal Prof. Peri Coronel - Sistema de Gestão Escolar

Sistema web completo para gestão escolar desenvolvido com Firebase, incluindo portais para administradores, professores e responsáveis.

## 🚀 Funcionalidades

### Portal do Administrador
- ✅ Gestão completa de **Notícias** (CRUD)
- ✅ Gestão de **Disciplinas** do boletim
- ✅ Gestão de **Turmas** e **Alunos**
- ✅ Cadastro e edição de **Professores**
- ✅ Cadastro e edição de **Responsáveis**
- ✅ **Alocação** de professores a turmas e disciplinas
- ✅ **Sistema de Ano Letivo** com histórico automático
- ✅ Finalização de ano letivo (move dados para histórico)

### Portal do Professor
- ✅ Visualização de turmas alocadas
- ✅ Lançamento de **notas** (4 bimestres)
- ✅ Lançamento de **faltas**
- ✅ Cálculo automático de médias
- ✅ Edição de perfil próprio

### Portal do Responsável
- ✅ Visualização de **boletim completo** dos filhos
- ✅ Consulta de notas por bimestre
- ✅ Consulta de faltas
- ✅ Acesso seguro via autenticação

### Site Público
- ✅ Informações institucionais (missão, visão, valores)
- ✅ **Notícias dinâmicas** carregadas do Firebase
- ✅ Formulário de contato
- ✅ Mapa de localização integrado
- ✅ Design responsivo

## 🛠️ Tecnologias

- **Frontend:** HTML5, CSS3, JavaScript ES6+
- **Backend:** Firebase (Authentication + Firestore)
- **Hospedagem:** Firebase Hosting
- **Versionamento:** Git + GitHub

## 📦 Estrutura do Projeto

```
CA3/
├── index.html              # Página principal pública
├── portal_professor.html   # Portal admin/professor
├── portal_responsavel.html # Portal dos responsáveis
├── styles.css              # Estilos globais
├── logo-lapis.png          # Logo (ícone)
├── logo-nome.png           # Logo (nome)
├── .gitignore              # Arquivos ignorados
└── README.md               # Documentação
```

## 🔥 Configuração do Firebase

### 1. Criar Projeto no Firebase Console
- Acesse https://console.firebase.google.com/
- Crie um novo projeto
- Ative **Authentication** (Email/Password)
- Ative **Firestore Database**

### 2. Configurar Firestore

#### Coleções necessárias:
- `config` - Configurações do sistema
- `usuarios` - Perfis de admin/professor/responsavel
- `alunos` - Dados dos alunos
- `turmas` - Turmas da escola
- `disciplinas` - Disciplinas do currículo
- `alocacoes` - Alocação de professores
- `notas` - Notas dos alunos (com campo `anoLetivo`)
- `faltas` - Faltas dos alunos (com campo `anoLetivo`)
- `noticias` - Notícias do site
- `historico` - Histórico de anos anteriores

#### Documento de configuração obrigatório:
```javascript
// Coleção: config
// Documento: sistema
{
  anoLetivoAtual: 2025
}
```

### 3. Regras de Segurança (Firestore Rules)

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Configurações (apenas leitura para autenticados)
    match /config/{document=**} {
      allow read: if request.auth != null;
      allow write: if request.auth != null && get(/databases/$(database)/documents/usuarios/$(request.auth.uid)).data.role == 'admin';
    }
    
    // Usuários
    match /usuarios/{userId} {
      allow read: if request.auth != null;
      allow write: if request.auth != null && (
        request.auth.uid == userId || 
        get(/databases/$(database)/documents/usuarios/$(request.auth.uid)).data.role == 'admin'
      );
    }
    
    // Alunos, turmas, disciplinas (admin apenas)
    match /{collection}/{document=**} {
      allow read: if request.auth != null;
      allow write: if request.auth != null && get(/databases/$(database)/documents/usuarios/$(request.auth.uid)).data.role == 'admin';
    }
    
    // Notícias públicas
    match /noticias/{noticia} {
      allow read: if true;
      allow write: if request.auth != null && get(/databases/$(database)/documents/usuarios/$(request.auth.uid)).data.role == 'admin';
    }
  }
}
```

## 🚀 Deploy

### Deploy no Firebase Hosting

```bash
# Instalar Firebase CLI
npm install -g firebase-tools

# Login no Firebase
firebase login

# Inicializar projeto
firebase init hosting

# Deploy
firebase deploy
```

## 👤 Primeiro Acesso

### Criar usuário admin manualmente:

1. Acesse o Firebase Console → Authentication
2. Adicione usuário:
   - Email: `admin@pericoronel.edu.br`
   - Senha: (defina uma senha forte)
3. Copie o UID do usuário
4. Vá em Firestore → Coleção `usuarios`
5. Crie documento com ID = UID:
```javascript
{
  nome: "Administrador",
  email: "admin@pericoronel.edu.br",
  role: "admin"
}
```

## 📊 Sistema de Ano Letivo

O sistema gerencia automaticamente os anos letivos:

- **Ano Atual:** Todas as notas/faltas são filtradas pelo ano atual
- **Histórico:** Mantém dados de 1 ano anterior
- **Finalização:** Botão "Finalizar Ano Letivo" move dados para histórico e limpa ano atual
- **IDs Únicos:** Notas e faltas incluem ano no ID: `{alunoId}_{disciplinaId}_{bimestre}_{ano}`

### Finalizar Ano Letivo (Admin):
1. Acesse Portal do Professor/Admin
2. Clique em "Finalizar Ano Letivo 2025"
3. Confirme com senha de admin
4. Sistema automaticamente:
   - Move notas/faltas de 2025 para `historico`
   - Apaga histórico de 2024
   - Incrementa ano para 2026
   - Alunos e turmas permanecem (rematricular manualmente)

## 🎨 Customização

### Alterar cores:
Edite as variáveis CSS em `styles.css`:
```css
:root {
  --color-primary: #5DA7E3;
  --color-secondary: #C92F2F;
  --color-text: #333;
  --color-bg: #f9f9f9;
}
```

### Alterar logos:
Substitua os arquivos:
- `logo-lapis.png` (ícone)
- `logo-nome.png` (nome da escola)

## 📝 Credenciais de Exemplo

**Admin:**
- Email: `admin@pericoronel.edu.br`
- Senha: (definida por você)

**Professor:**
- Email: `professor@pericoronel.edu.br`
- Senha: (cadastrado pelo admin)

**Responsável:**
- Email: `responsavel@pericoronel.edu.br`
- Senha: (cadastrado pelo admin)

## 🔒 Segurança

- ✅ Autenticação Firebase
- ✅ Regras de segurança Firestore
- ✅ Validação de email duplicado
- ✅ Senhas armazenadas com hash (Firebase Auth)
- ✅ Separação de roles (admin/professor/responsavel)

## 📞 Suporte

Para dúvidas ou problemas, entre em contato:
- 📧 Email: contato@pericoronel.edu.br
- 📞 Telefone: (53) 3241-8607
- 📍 Endereço: Rua Getulio de Souza Pereira, 150 - Bagé/RS

## 📄 Licença

Este projeto foi desenvolvido para uso exclusivo da Escola Municipal Prof. Peri Coronel.

---

**Desenvolvido com ❤️ para educação de qualidade**
