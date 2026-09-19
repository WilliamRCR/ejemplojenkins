# Ejemplo práctico: Pipeline CI con Jenkins

Proyecto Python (calculadora) con pruebas unitarias. Jenkins descarga el código,
instala dependencias, revisa el estilo (flake8), ejecuta pruebas (pytest) y empaqueta.

## 1. Levantar Jenkins (Docker)
```bash
docker compose up -d --build
docker exec jenkins-demo cat /var/jenkins_home/secrets/initialAdminPassword
```
Abrir http://localhost:9090, pegar la clave, elegir **Install suggested plugins** y crear el usuario admin.

## 2. Subir el proyecto a GitHub
Crear un repositorio y subir el contenido de esta carpeta (`Jenkinsfile` en la raíz).

## 3. Crear el job
1. **New Item** → nombre `calculadora-ci` → **Pipeline** → OK.
2. En *Pipeline*: Definition = **Pipeline script from SCM**, SCM = **Git**,
   Repository URL = tu repo, Branch = `*/main`, Script Path = `Jenkinsfile`.
3. Guardar y pulsar **Build Now**.

## 4. Demostración (para la presentación)
1. **Build exitoso**: todas las etapas en verde, se ve el reporte de pruebas y el `.tar.gz` archivado.
2. **Romper una prueba**: en `app/calculadora.py` cambiar `return a + b` por `return a - b`, hacer commit y push.
3. Jenkins detecta el cambio (polling cada 2 min o **Build Now**): la etapa *Pruebas unitarias* se pone en rojo
   y el pipeline se detiene.
4. Revertir el cambio: el pipeline vuelve a verde.

## Ejecutar localmente sin Jenkins
```bash
python -m venv .venv && . .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -r requirements.txt
flake8 app tests && pytest --cov=app
```
