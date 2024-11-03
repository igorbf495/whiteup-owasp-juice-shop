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

