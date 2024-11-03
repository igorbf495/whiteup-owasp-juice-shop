Usando a feramenta dirb, encontrei arquivos com informações confidênciais:
![image](https://github.com/user-attachments/assets/64b93620-0a2e-4f6d-a8d6-e396f8cee894)

/robots.txt
/.well-known/security.txt
/ftp

XSS Dom based encontrado na barra de pesquisa de produtos:
![image](https://github.com/user-attachments/assets/3953128b-66fc-46eb-abbc-e779822f0110)


payload: <iframe src="javascript:alert('teste')">

ao navegar até o path /track-result vi que ele passa um id na query string como uma entrada de dados, ou seja, se tem input pode conter um possível xss, não?

![image](https://github.com/user-attachments/assets/94cd8e85-ea51-4d5e-8f56-d66f8e71ab4e)

tentei o memso payload do iframe e...
![image](https://github.com/user-attachments/assets/a3d7205b-040b-4e12-910d-73aacebac132)

olha lá, conseguimos ativar o motor javascript do navegador, logo, encontramos um XSS Reflected

![image](https://github.com/user-attachments/assets/dd3f0ec8-e2af-4d92-9582-de291b904bed)

no endpoint /administration#/contact encontrei uma vulnerabilidade A01:2021-Broken Access Control, A04:2021 – Insecure Design e A03:2021 – Injection

Notei que existe uma certa validação de entrada, é obrigário votar nas estrelas para conseguir publicar um comentário:
![image](https://github.com/user-attachments/assets/a774a46d-498a-4e83-8478-a87fc40b7bd6)

usei a dev tools para procurar se essa validação é feita do lado do cliente (navegador) ou no lado do server

![image](https://github.com/user-attachments/assets/a6feda3a-af15-446d-850d-020962f008ef)

olha só oq encontrei... então pensei, o que acontece se eu alterar esse parâmetro de disable para enable?

![image](https://github.com/user-attachments/assets/5752f037-794a-4629-9863-22a1486174e3)

deu certo, burlamos a validação do botão, será que vamos conseguir enviar comentário sem estrela?

![image](https://github.com/user-attachments/assets/89ee4912-7ef5-4534-ab67-3cb6fec13d61)

feedback enviado.

Inseguranças no design ocorrem quando o sistema é projetado sem as proteções adequadas para cenários possíveis. Nesse caso, confiar na validação do frontend para forçar uma avaliação mínima de uma estrela é um design inseguro.




