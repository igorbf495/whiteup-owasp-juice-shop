Relatório de Vulnerabilidades – OWASP Juice Shop

Resumo:
Este relatório detalha várias vulnerabilidades críticas no OWASP Juice Shop, categorizadas conforme o OWASP Top 10. Foram identificadas falhas de Broken Access Control, Cross-Site Scripting (XSS) e Insecure Design, que comprometem a segurança e a integridade da aplicação.
<br>
<br>
Enumeração de Diretórios Sensíveis
<br>
A05:2021 – Security Misconfiguration

Descrição:
A ferramenta DirHunter revelou diretórios que contêm informações sensíveis:

/robots.txt
/.well-known/security.txt
/ftp

Impacto:
Esses arquivos expõem informações que podem auxiliar um atacante a identificar endpoints críticos e potenciais pontos de exploração.

![image](https://github.com/user-attachments/assets/64b93620-0a2e-4f6d-a8d6-e396f8cee894)
<br>
<br>
XSS DOM-Based na Barra de Pesquisa de Produtos

A03:2021 – Injection

Descrição:
Um Cross-Site Scripting (XSS) DOM-Based foi encontrado na barra de pesquisa de produtos. A entrada do usuário não é sanitizada adequadamente, permitindo a execução de código JavaScript.

Passos para Reproduzir:

Acesse a barra de pesquisa.
Insira o payload:
  <iframe src="javascript:alert('teste')">

A execução do código JavaScript é acionada.

Impacto:
Permite que scripts maliciosos sejam executados no navegador do usuário, comprometendo a integridade da aplicação.

![image](https://github.com/user-attachments/assets/3953128b-66fc-46eb-abbc-e779822f0110)

<br>
<br>
XSS Reflected no Endpoint /track-result

A03:2021 – Injection

Descrição:
O endpoint /track-result apresenta uma vulnerabilidade de XSS refletido, permitindo a execução de JavaScript via o parâmetro id.

Passos para Reproduzir:

Navegue até /track-result?id=<payload>.
Use o payload:
  <iframe src="javascript:alert('XSS')">

O JavaScript é executado no navegador.

Impacto:
Scripts maliciosos podem ser refletidos e executados, comprometendo os usuários.


![image](https://github.com/user-attachments/assets/94cd8e85-ea51-4d5e-8f56-d66f8e71ab4e)

tentei o memso payload do iframe e...
![image](https://github.com/user-attachments/assets/a3d7205b-040b-4e12-910d-73aacebac132)

olha lá, conseguimos ativar o motor javascript do navegador, logo, encontramos um XSS Reflected

![image](https://github.com/user-attachments/assets/dd3f0ec8-e2af-4d92-9582-de291b904bed)
<br>
<br>
Bypass de Validação de Feedback (Broken Access Control)

A01:2021 – Broken Access Control
A04:2021 – Insecure Design

Descrição:
O feedback exige a seleção de estrelas, mas a validação ocorre apenas no frontend. Através do DevTools, o atributo disabled foi modificado, permitindo enviar um feedback sem estrelas.

Passos para Reproduzir:

Abra o DevTools e remova o atributo disabled do botão.
Envie o feedback sem selecionar estrelas.

Impacto:
A ausência de validação no backend compromete a integridade dos dados de feedback.

![image](https://github.com/user-attachments/assets/a774a46d-498a-4e83-8478-a87fc40b7bd6)

usei a dev tools para procurar se essa validação é feita do lado do cliente (navegador) ou no lado do server

![image](https://github.com/user-attachments/assets/a6feda3a-af15-446d-850d-020962f008ef)

então pensei, o que acontece se eu alterar esse parâmetro de disable para enable?

![image](https://github.com/user-attachments/assets/5752f037-794a-4629-9863-22a1486174e3)

deu certo, burlamos a validação do botão, será que vamos conseguir enviar comentário sem estrela?

![image](https://github.com/user-attachments/assets/89ee4912-7ef5-4534-ab67-3cb6fec13d61)

feedback enviado.

Inseguranças no design ocorrem quando o sistema é projetado sem as proteções adequadas para cenários possíveis. Nesse caso, confiar na validação do frontend para forçar uma avaliação mínima de uma estrela é um design inseguro.
<br>
<br>
Manipulação de Avaliação de Usuário (Forged Review)

A01:2021 – Broken Access Control

Descrição:
Ao enviar uma avaliação de produto, o campo author pode ser modificado, permitindo falsificar o autor da avaliação.

Passos para Reproduzir:

Intercepte a requisição POST para /rest/products/1/reviews.
Modifique o campo author para um e-mail alternativo.
Envie a requisição.

Impacto:
Permite a falsificação de avaliações, comprometendo a autenticidade dos feedbacks.

![image](https://github.com/user-attachments/assets/cd22604c-c120-40d9-bb23-7bb6f608a95e)

![image](https://github.com/user-attachments/assets/84ef5964-b46d-4859-83b4-24e22b59f72a)

enviei um comentário de teste usando o burp suite para interceptar a request

![image](https://github.com/user-attachments/assets/4b9b7882-e4d3-4088-9fae-4884f1a60862)

agora, vamos para o burp analisar como a aplicação se comporta por trás das câmeras

![image](https://github.com/user-attachments/assets/bdca5642-dc4c-47a6-afe3-32b7a14bfa69)

Nota-se que temos o parâmetro message e author, mas aos olhos de como deve funcionar, só temos um input para receber os dados do message e não do author. Mas o que acontece se eu tentar alterar esse author antes da requisião chegar lá?

Usando o burp suite como proxy para interceptar eses request alterei o author

![image](https://github.com/user-attachments/assets/abaa7dfa-c5bb-4d80-a690-bdc61edcfa3c)

após dar enter, voltei ao navegador para verificar oq aconteceu e conseguimos mais uma vez.

![image](https://github.com/user-attachments/assets/9b792cf1-0c1b-479c-903f-6c5b123255dd)

Encontramos mais uma vulnerabilidade de Broken Access Control.
<br>
<br>
Registro de Conta com Privilégios de Administrador

A01:2021 – Broken Access Control

Descrição:
Durante o registro, o campo role pode ser modificado para criar uma conta com privilégios de administrador, ignorando as restrições normais.

Passos para Reproduzir:

Intercepte a requisição POST para /api/Users ao registrar um novo usuário.
Adicione o campo "role": "admin" no payload da requisição.
Envie a requisição modificada.

Impacto:
Permite que usuários comuns se registrem como administradores, comprometendo a segurança da aplicação.


simulei um registro de um user:

![image](https://github.com/user-attachments/assets/45b30949-13bc-4189-bee7-71f7dcef74ae)

vamos analistar por trás das cameras como está sendo feita essa resquest

![image](https://github.com/user-attachments/assets/7fa571ac-5d20-4f64-8f55-b90ac06f7be2)

vemos que estamos mandando um POST para /api/users quando vamos criar um usuário comum. Nota-se que o server nos deu uma resposta, se analisarmos o corpo, ele nos mostra um parâmetro que nao tem no lado do cliente. Mas será mesmo? Vamo ver se está fazendo as validações corretas. Bora adicionar esse parâmtro role que encontramos na reposta, mas, no lugar de costumer, irei colocar "admin".

![image](https://github.com/user-attachments/assets/24593214-02b1-40a8-bd98-ff620e23451c)

dei enter. Vamos lá conferir se criamos o usuário com permissões de admin

![image](https://github.com/user-attachments/assets/cb68bb1e-cb5f-4047-9f98-164f6622d2bf)

olhá só, retornou 200, vamos conferir no navegador

![image](https://github.com/user-attachments/assets/5c670591-06cf-4f21-951e-229bdfabe865)


logamos.

![image](https://github.com/user-attachments/assets/f9e905fd-9c08-4f43-b2e7-4b5f00b1b94c)
<br>
<br>
Conclusão e Recomendações
<br>
Resumo das Vulnerabilidades Identificadas:

Security Misconfiguration em arquivos e diretórios públicos.
Vulnerabilidades de Injection (XSS DOM-Based e Reflected).
Broken Access Control e Insecure Design em validações e permissões de feedbacks e contas.
Recomendações:

Aplicar validações de acesso no backend: Assegure que valores de role e author sejam controlados pelo servidor.
Sanitizar todas as entradas de usuário: Para prevenir ataques XSS, todas as entradas devem ser sanitizadas.
Melhorar a segurança de diretórios públicos: Proteja arquivos que contenham informações sensíveis.
Fortalecer as validações de feedbacks: Assegure que feedbacks inválidos não sejam processados pelo servidor.

