Correção: “Quais são os nomes dos Helm charts do CRM e do Insights, então?”

Não há, oficialmente, charts separados chamados:

frappe/crm
frappe/insights

O chart oficial que eu usaria para ambos é:

frappe/erpnext

Apesar do nome, esse chart cria um ambiente Frappe Bench-like em Kubernetes, com gunicorn, nginx, scheduler, socketio, workers, jobs de site/migrate/backup, PVCs e services. O repo oficial é adicionado assim, e o exemplo oficial instala frappe/erpnext.

Então, a diferença não é o chart. A diferença é o release name, a imagem Docker e a app instalada.

helm repo add frappe https://helm.erpnext.com
helm repo update

Para o CRM:

helm upgrade --install frappe-crm frappe/erpnext \
  -n frappe-crm \
  --create-namespace \
  -f values-crm.yaml

Com imagem:

image:
  repository: ghcr.io/frappe/crm
  tag: stable

A documentação oficial do Frappe CRM usa ghcr.io/frappe/crm e --app=crm no self-hosted production setup.

Para o Insights:

helm upgrade --install frappe-insights frappe/erpnext \
  -n frappe-insights \
  --create-namespace \
  -f values-insights.yaml

Com imagem:

image:
  repository: ghcr.io/frappe/insights
  tag: stable

A documentação oficial do Frappe Insights usa ghcr.io/frappe/insights e --app=insights no self-hosted production setup.

Resumo:

Helm chart CRM:      frappe/erpnext
Release name CRM:   frappe-crm
Docker image CRM:   ghcr.io/frappe/crm
App:                crm

Helm chart Insights:      frappe/erpnext
Release name Insights:   frappe-insights
Docker image Insights:   ghcr.io/frappe/insights
App:                     insights

Ou seja: usas o mesmo chart frappe/erpnext duas vezes, uma vez para CRM e outra vez para Insights.