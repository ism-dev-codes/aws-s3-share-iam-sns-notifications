# ☁️ Working with Amazon S3: Secure Sharing, IAM Governance & Event Notifications

> ℹ️ **NOTE:** Este é o repositório desenvolvido por **Ismael Santos de Medeiros**.

Projeto prático focado na criação, governança de acesso e automação de notificações no **Amazon Simple Storage Service (Amazon S3)**. O laboratório simula um cenário real de compartilhamento seguro de ativos de mídia de um café com um usuário externo (`mediacouser`), gerenciando permissões granulares via **AWS IAM**, operando requisições via **AWS CLI** e integrando o armazenamento ao **Amazon Simple Notification Service (Amazon SNS)** para envio automatizado de alertas por e-mail a cada alteração no bucket.

---

## 💻 Tecnologias utilizadas no projeto

- [Amazon S3 (Simple Storage Service)](https://aws.amazon.com/s3/) — Armazenamento de objetos seguro, altamente disponível e escalável
- [Amazon SNS (Simple Notification Service)](https://aws.amazon.com/sns/) — Serviço de mensageria para envio de notificações pub/sub por e-mail
- [AWS IAM (Identity and Access Management)](https://aws.amazon.com/iam/) — Controle de acesso granular, políticas de segurança e gerenciamento de chaves de acesso
- [AWS CLI](https://aws.amazon.com/cli/) — Interface de linha de comando para automação das operações no S3 e gerenciamento de eventos
- [Amazon EC2 (CLI Host)](https://aws.amazon.com/ec2/) — Instância Linux utilizada para execução de comandos administrativos via EC2 Instance Connect

---

## ✨ Como foi feito ?

- Conectado à instância EC2 `CLI Host` via **EC2 Instance Connect** e configuradas as credenciais administrativas do **AWS CLI** na região `us-west-2` (Oregon).
- Criado um bucket S3 exclusivo (`cafe-ismaelmedeiros`) via comando `aws s3 mb` e realizada a carga inicial de imagens sob o prefixo `images/` utilizando `aws s3 sync`.
- Auditadas as políticas do grupo IAM `mediaco` (`mediaCoPolicy`) para validar as permissões de leitura, gravação e exclusão limitadas estritamente ao diretório `images/*`.
- Geradas novas chaves de acesso programático (*Access Keys*) para o usuário `mediacouser` e realizado teste de login no console e navegação restrita.
- Testados no console AWS e via linha de comando os casos de uso do parceiro de mídia: visualização (`GetObject`), upload (`PutObject`) e exclusão de objetos (`DeleteObject`).
- Criado e configurado o tópico **Amazon SNS** `s3NotificationTopic` com Política de Acesso ajustada para permitir publicações originadas do Amazon S3.
- Subscrito um endereço de e-mail ao tópico SNS e confirmada a assinatura através do e-mail de verificação recebido.
- Criado o arquivo de configuração `s3EventNotification.json` e associado ao bucket via `aws s3api put-bucket-notification-configuration` para disparar alertas em eventos de criação e remoção de objetos (`ObjectCreated:*` e `ObjectRemoved:*`).
- Configurado a AWS CLI com as credenciais de `mediacouser` e efetuado o upload da nova imagem `Caramel-Delight.jpg` via `aws s3api put-object`.
- Validado o recebimento do e-mail de notificação em tempo real detalhando o evento `ObjectCreated:Put` gerado pelo Amazon S3.
- Realizado o teste de exclusão do objeto `Strawberry-Tarts.jpg` e validado o e-mail de alerta para o evento `ObjectRemoved:Delete`.
- Testado e confirmado o bloqueio de ações não autorizadas via CLI ao tentar alterar a ACL do objeto (`aws s3api put-object-acl --acl public-read`), resultando no erro esperado `AccessDenied`.

---

## 📚 Materiais

- Documentação do Amazon S3: [Amazon S3 User Guide](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html)
- Guia do AWS CLI para S3: [AWS CLI S3 Commands Reference](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3/index.html)
- Notificações de Eventos do S3: [Configuring Amazon S3 Event Notifications](https://docs.aws.amazon.com/AmazonS3/latest/userguide/EventNotifications.html)
- Documentação do Amazon SNS: [Amazon Simple Notification Service Developer Guide](https://docs.aws.amazon.com/sns/latest/dg/welcome.html)

---

## 🛠️ Instruções de execução

Para replicar os procedimentos de gerenciamento e automação no Amazon S3, siga as etapas detalhadas abaixo.

- 🤖 1. No console do Amazon EC2, localize a instância `CLI Host` preparada para administração do ambiente.

![Instância CLI Host](Amazon%20S3%20%281%29.png)

- 🤖 2. Conecte-se à instância via terminal do navegador com o **EC2 Instance Connect**.

![Conexão EC2 Instance Connect](Amazon%20S3%20%282%29.png)

- 🤖 3. Execute o comando `aws configure` e insira as credenciais do ambiente para inicializar a AWS CLI.

![Configuração da AWS CLI](Amazon%20S3%20%284%29.png)

- 🤖 4. Crie o bucket S3 de compartilhamento utilizando o comando `aws s3 mb s3://cafe-ismaelmedeiros --region 'us-west-2'`.

![Criação do Bucket S3](Amazon%20S3%20%286%29.png)

- 🤖 5. Acesse o console do Amazon S3 e confirme a criação do novo bucket na lista de buckets.

![Confirmação do Bucket no Console](Amazon%20S3%20%288%29.png)

- 🤖 6. Sincronize o diretório de imagens locais para o bucket com `aws s3 sync ~/initial-images/ s3://cafe-ismaelmedeiros/images`.

![Sincronização de Arquivos](Amazon%20S3%20%289%29.png)

- 🤖 7. Verifique a listagem e o tamanho total dos objetos sincronizados com `aws s3 ls s3://cafe-ismaelmedeiros/images/ --human-readable --summarize`.

![Listagem de Objetos via CLI](Amazon%20S3%20%2810%29.png)

- 🤖 8. No console do IAM, navegue até os detalhes do usuário `mediacouser` pertencente ao grupo `mediaco`.

![Usuário IAM mediacouser](Amazon%20S3%20%2811%29.png)

- 🤖 9. Gere uma nova chave de acesso (*Access Key*) para uso na AWS CLI selecionando a opção **Command Line Interface (CLI)**.

![Criação de Access Key IAM](Amazon%20S3%20%2812%29.png)

- 🤖 10. Faça login no console da AWS utilizando a conta e credenciais do usuário restrito `mediacouser`.

![Login Console mediacouser](Amazon%20S3%20%2814%29.png)

- 🤖 11. Navegue até o bucket `cafe-ismaelmedeiros` e confirme a visualização dos arquivos sob a pasta `images/`.

![Navegação mediacouser no S3](Amazon%20S3%20%2817%29.png)

- 🤖 12. Teste a permissão de escrita realizando o upload manual de uma imagem (`croissant.jpg`) pelo console.

![Upload de Imagem Console](Amazon%20S3%20%2820%29.png)

- 🤖 13. Teste a exclusão de um arquivo confirmando a remoção de `Cup-of-Hot-Chocolate.jpg`.

![Exclusão de Objeto Console](Amazon%20S3%20%2824%29.png)

- 🤖 14. No console do Amazon SNS, crie um novo tópico do tipo padrão chamado `s3NotificationTopic`.

![Criação de Tópico SNS](Amazon%20S3%20%2829%29.png)

- 🤖 15. Edite a política de acesso (*Access Policy*) do tópico para conceder permissão de publicação ao serviço `s3.amazonaws.com`.

![Política de Acesso SNS](Amazon%20S3%20%2831%29.png)

- 🤖 16. Crie uma assinatura do protocolo **Email** e confirme o e-mail de verificação recebido na caixa de entrada.

![Assinatura SNS Confirmada](Amazon%20S3%20%2835%29.png)

- 🤖 17. No terminal da instância `CLI Host`, crie o arquivo `s3EventNotification.json` especificando o filtro de eventos de criação/remoção de objetos.

![Edição da Notificação JSON](Amazon%20S3%20%2837%29.png)

- 🤖 18. Associe o arquivo de eventos ao bucket com o comando `aws s3api put-bucket-notification-configuration`.

![Associação da Notificação no S3](Amazon%20S3%20%2839%29.png)

- 🤖 19. Verifique a caixa de e-mail para confirmar o recebimento da mensagem de teste (`s3:TestEvent`) enviada automaticamente pelo Amazon S3.

![E-mail de Teste SNS](Amazon%20S3%20%2840%29.png)

- 🤖 20. Reconfigure a AWS CLI com as credenciais do `mediacouser` e execute o upload do arquivo `Caramel-Delight.jpg` via `aws s3api put-object`.

![Upload via CLI mediacouser](Amazon%20S3%20%2842%29.png)

- 🤖 21. Valide no e-mail o recebimento da notificação do Amazon SNS com o evento `ObjectCreated:Put` gerado para a nova imagem.

![Notificação E-mail ObjectCreated](Amazon%20S3%20%2845%29.png)

- 🤖 22. Teste a exclusão do objeto via CLI, confirme o recebimento do alerta `ObjectRemoved:Delete` por e-mail e valide o bloqueio de alterações na ACL (`PutObjectAcl` Access Denied).

![Exclusão CLI e Bloqueio de ACL](Amazon%20S3%20%2851%29.png)

---

## 🏁 Conclusão

Este projeto demonstrou a implementação de uma arquitetura de **compartilhamento de arquivos segura, governada e reativa** na nuvem AWS. 

Através do gerenciamento de políticas do IAM, foi possível aplicar o princípio do **privilégio mínimo (Least Privilege)**, permitindo que usuários externos executem manipulações necessárias no bucket sem comprometer as configurações de segurança globais do recurso. A integração nativa do Amazon S3 com o Amazon SNS garantiu total **visibilidade e auditoria**, notificando automaticamente os administradores sobre modificações em tempo real sem a necessidade de servidores dedicados para monitoramento.

---

## 👨‍💻 Expert

<p>
    <img 
      align=left 
      margin=10 
      width=80 
      src="https://avatars.githubusercontent.com/u/105826184?v=4"
    />
    <p>&nbsp&nbsp&nbspIsmael Medeiros<br>
    &nbsp&nbsp&nbsp
    <a 
        href="https://github.com/ism-dev-codes">
        GitHub
    </a>
    &nbsp;|&nbsp;
    <a 
        href="https://www.linkedin.com/in/ismael-medeiros">
        LinkedIn
    </a>
    &nbsp;|&nbsp;
    <a 
        href="https://www.instagram.com/ismaelsmedeiros?igsh=YXA1OW1mNXhkNmVy">
        Instagram
    </a>
    &nbsp;|&nbsp;</p>
</p>
<br/><br/>
<p>
