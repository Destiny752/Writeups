### Enumeración
Empezamos con el despliegue de la máquina vulnerable
![[Pasted image 20260402134825.png]]
Seguido de esto hacemos un escaneo para encontrar puertos que tiene
nmap -sCV -p- 172.17.0.2
![[Pasted image 20260402135508.png]]
![[Pasted image 20260402135533.png]]
Entramos a la pagina para ver que hay
![[Pasted image 20260402135657.png]]
Parece todo normal pero abajo a la derecha hay un error escrito, con esto nos da a entender que la validación por parte del servidor a la web es débil por eso podemos penar que seria posible ejecutar un LFI o RFI. **Ese “ERROR [ ]” NO significa directamente que exista LFI o RFI, pero sí indica que la aplicación está intentando cargar algo dinámicamente**
### Fuzzing web
Pero segumos con la metodología y aplicamos un fuzzing en la web.
![[Pasted image 20260402140403.png]]
Index.php es donde estabamos antes vamos a ver que hay dentro de assets
![[Pasted image 20260402140453.png]]

No parece que haya mucho asi que vamos a intentar explotar una vulnerabilidad LFI,
con curl vamos a intentar ver si podemos ejecutar algun comando
![[Pasted image 20260402141250.png]]
pero no nos reporta nada con cmd ni con file asi que vamos a hacer FUZZ en el aparatado que queremos encontrar la consola
![[Pasted image 20260402154130.png]]
Vamos a probar primero con un LFI si no hay suerte probaremos con un RFI
![[Pasted image 20260402155521.png]]
Ponemos -fs para ocultar tamaños iguales a 2596 sino saldría algo como esto:
![[Pasted image 20260402155647.png]]

![[Pasted image 20260402155709.png]]

y hay lo encontramos
![[Pasted image 20260402155731.png]]
Vemos que hay dos suarios en la maquina como tenemos un LFI podemos intentar sacar el el id_rsa 

%%`id_rsa` es **la clave privada SSH** utilizada para autenticarte de forma segura en servidores remotos sin necesidad de contraseña.  
Forma parte de un **par de claves**:

- **id_rsa** → _clave privada_ (se guarda en tu equipo, nunca debe compartirse)%%

De luisillo no reporta nada
![[Pasted image 20260402173135.png]]
así que probamos del otro usuario llamado vaxei
![[Pasted image 20260402173326.png]]
Ahí nos reporta su clave privada
![[Pasted image 20260402174513.png]]
Le damos permisos 600 que sirve para:
- **6 (rw-) para el propietario** → puede **leer y escribir**
- **0 (---) para el grupo** → sin permisos
- **0 (---) para otros** → sin permisos
Con esto ya podemos entrar via ssh sin otorgar contraseña
![[Pasted image 20260402174546.png]]
