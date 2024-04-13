
Criar grupo de alerta

O envio de por email pode ser realizado para grupos, porem deve ser solicitado a infra conforme chamado 13747

`alertmanager_receivers:` Criar os webhook e email, aqui cria o grupo propriamente dito.
```
- name: 'sup24x7-team-telegram'
  webhook_configs:
  - url: 'http://localhost:9876/alert'  
- name: 'sup24x7-team-mail'
  email_configs:
  - to: 'mail@ndd.com.br'

`_Template para os alertas_`

  - alert: URL-Indisponivel
    expr: probe_success{produto=~"frete"} == 0
    for: 60s
    labels:
      severity: critical
      sre_team: "yes"  # este campo yes vai direcionar todos os alertas para o match sre
      frete_team: "yes"
    annotations:
      summary: "sempre que uma URL fica sem responder por mais de 60 segundos."
      description: "O ambiente {{ $labels.ambiente }} está com o endereço ({{ $labels.instance }}) indisponível"
      acao: 'Será necessário conectar no servidor e verificar o que provocou a queda.'
      url: "{{ $labels.instance }}"
      ambiente: "{{ $labels.ambiente }}"
  
```

 `alertmanager_route:` Criar a rota, destino dos aleras, email, telegram por exemplo.

```
- match:
      sup24x7_team: "yes"
    receiver: "sup24x7-team-mail"
    continue: true
    routes:
    - receiver: "sup24x7-team-telegram"
      continue: true
    - receiver: "sup24x7-team-mail"
      continue: true
```
`sachet_receivers:` Grupos do Sachet, telegram

```
  provider: 'telegram'
  to: # the chat id of a user. Get yours at https://telegram.me/userinfobot
  - '1557182610' # ID Telegram
  text: '{{ "{{" }}  template "telegram_message" . {{ "}}" }}'
  ```
