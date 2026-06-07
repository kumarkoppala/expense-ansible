# expense-ansible
```
my-ansible-project/
├── inventory.ini             # Your list of frontend/backend servers
├── playbook.yml              # Your main execution file
├── group_vars/               # 👈 ROOT LEVEL (Not inside roles)
│   ├── all.yml               # 🌐 Applies to ALL servers and ALL roles
│   ├── frontend.yml          # 💻 Applies ONLY to frontend hosts
│   └── backend.yml           # ⚙️ Applies ONLY to backend hosts
└── roles/
    ├── web_server/           # Role for frontend services
    │   ├── tasks/main.yml
    │   └── defaults/main.yml # 👈 Role defaults go here, NOT group_vars
    └── db_server/            # Role for database services
        ├── tasks/main.yml
        └── defaults/main.yml
```
