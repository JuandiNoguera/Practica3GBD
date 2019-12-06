MongoDB:

1. Averigua si existe la posibilidad en MongoDB de limitar el acceso de un usuario a los datos de una colección determinada.

- Teniendo una coleccion con dos tablas
```
> use Medicamentos
switched to db Medicamentos

> show collections
Medicamentos
PrincipiosActivos

```
- Le damos al usuario Jose acceso solo de lectura al usuario sobre la tabla PrincipiosActivos de la coleccion Medicamentos
```
> db.createUser(
... {
... user: "Jose",
... pwd: "Jose",
... roles:[{role: "read", db: "PrincipiosActivos"}]
... })

> show users
{
	"_id" : "Medicamentos.Jose",
	"userId" : UUID("69f24bf7-bf53-4785-ac5f-99ea5b5cecaf"),
	"user" : "Jose",
	"db" : "Medicamentos",
	"roles" : [
		{
			"role" : "read",
			"db" : "PrincipiosActivos"
		}
	],
	"mechanisms" : [
		"SCRAM-SHA-1",
		"SCRAM-SHA-256"
	]
```
       
2. Averigua si en MongoDB existe el concepto de privilegio del sistema y muestra las diferencias más importantes con ORACLE.

- Los privilegios del sistema en mongoDB dependen del rol que tenga el usuario asignado(los roles estan explicados en el siguiente punto).

| MongoDB | Oracle         |
| ------- | -------------- |
| db.createCollection()        | create table   |
| db.createView()        | create view    |
| db.createUser()        | create user    |
| db.startSession()        | create session |
| db.dropRole()        | drop role      |
| db.dropUser()        | drop user      |


3. Explica los roles por defecto que incorpora MongoDB y como se asignan a los usuarios.

**• Roles de usuarios:**
         **◦ A nivel de una base de datos**
             ▪ read: Solo lectura
             ▪ readWrite: Lectura y escritura
        **◦ Todas las bases de datos**
             ▪ readAnyDatabase: Solo lectura en todas la base de datos creadas
             ▪ readWriteAnyDatabase: Lectura y escritura en todas las bases de datos
 
**• Roles de administración de base de datos (sobre la base de datos que se le asigne)**
        ◦ dbAdmin: Permite la gestión de los datos pero no acceder a la información sobre los usuarios. Puede realizar las siguientes acciones:
            ▪ collStats 
            ▪ dbHash 
            ▪ dbStats 
            ▪ killCursors 
            ▪ listIndexes 
            ▪ listCollections 
            ▪ bypassDocumentValidation 
            ▪ collMod
            ▪ collStats 
            ▪ compact 
            ▪ convertToCapped
            ▪ createCollections
            ▪ dropCollections
        ◦ userAdmin: Permite crear, modificar y eliminar usuarios o roles, además de otorgar privilegios. Puede realizar las siguientes acciones:
            ▪ changeCustomData 
            ▪ changePassword 
            ▪ createRole 
            ▪ createUser 
            ▪ dropRole 
            ▪ dropUser 
            ▪ grantRole 
            ▪ revokeRole 
            ▪ setAuthenticationRestriction 
            ▪ viewRole 
            ▪ viewUser 
        ◦ dbOwner: Permite realizar cualquier operación de administración en la base de datos, junta los privilegios de readWrite, dbAdmin y userAdmin
    **• Roles de administración de base de datos (sobre todas las bases de datos)**
        ◦ dbAdminAnyDatabase: Permite la gestión de los datos pero no acceder a la información sobre los usuarios
        ◦ userAdminAnyDatabase:  Permite crear, modificar y eliminar usuarios o roles, además de otorgar privilegios
    **• Roles de administración de cluster**
        ◦ clusterAdmin 
        ◦ clusterManager 
        ◦ clusterMonitor 
        ◦ hostManager 
    **• Roles de copia de seguridad y restauración**
        ◦ backup
        ◦ restore
    **• Rol de super usuario**
        ◦ root
	
Asignación de roles
    • Creación de usuarios
    
```
db.createUser (
    {
        user: ‘juandi’,
        pwd: ‘juandi’,
        roles: [ { db: ‘NombreBaseDatos’ ,  role: ‘Rol’ } ]
    }
  )
• Ejemplo:
    db.createUser (
        {
            user: ‘juandi’,
            pwd: ‘juandi’,
            roles: [ { db: ‘Medicinas’ ,  role: ‘readWrite’ } ]
        }
    )
```
4. Explica como puede consultarse el diccionario de datos de MongoDB para saber que roles han sido concedidos a un usuario y qué privilegios incluyen.

- Con *show users* podemos consultar los roles asignados a los usuarios
ejemplo:

```
{
	"_id" : "admin.antonio",
	"userId" : UUID("a1a9151b-adc2-4a02-9d9d-d6ee0c21a970"),
	"user" : "antonio",
	"db" : "admin",
	"roles" : [
		{
			"role" : "read",
			"db" : "admin"
		}
	],
	"mechanisms" : [
		"SCRAM-SHA-1",
		"SCRAM-SHA-256"
	]
}
{
	"_id" : "admin.juandi",
	"userId" : UUID("76d15fde-8421-48d8-92ee-cdadfcc2ee4d"),
	"user" : "juandi",
	"db" : "admin",
	"roles" : [
		{
			"role" : "readWrite",
			"db" : "admin"
		}
	],
	"mechanisms" : [
		"SCRAM-SHA-1",
		"SCRAM-SHA-256"
	]
}
{
	"_id" : "admin.carvajal",
	"userId" : UUID("7a985d2b-3696-498d-85ca-5ab3d39fd843"),
	"user" : "carvajal",
	"db" : "admin",
	"roles" : [
		{
			"role" : "readWrite",
			"db" : "Medicamentos"
		}
	],
	"mechanisms" : [
		"SCRAM-SHA-1",
		"SCRAM-SHA-256"
	]
}
```
- Para ver los privilegios de cada rol usamos db.getRole("read", { showPrivileges: true })

ORACLE:

   1. Realiza un procedimiento llamado MostrarObjetosAccesibles que reciba un nombre de usuario y muestre todos los objetos a los que tiene acceso.

```
create or replace procedure MostrarObjetosAccesibles(p_usuario VARCHAR2)
is
    v_privilegio    VARCHAR2(50);
    v_tabla         VARCHAR2(50);
    v_propietario   VARCHAR2(50);
    cursor c_cursor is
    select table_name, privilege, owner
    from dba_tab_privs
    where grantee=p_usuario;
    v_cursor c_cursor%ROWTYPE;
begin
	for v_cursor in c_cursor loop
		v_tabla:=v_cursor.table_name;
		v_privilegio:=v_cursor.privilege;
		v_propietario:=v_cursor.owner;
    	dbms_output.put_line('Nombre de la tabla: '||v_tabla||' Privilegio concedido: '||v_privilegio||' Propietario: '||v_propietario);
    end loop;
end;
/
```

Prueba de funcionamiento
```
SQL> exec MostrarObjetosAccesibles('JUANDI')
Nombre de la tabla: DEPT Privilegio concedido: UPDATE Propietario: SCOTT
Nombre de la tabla: DEPT Privilegio concedido: SELECT Propietario: SCOTT
Nombre de la tabla: DEPT Privilegio concedido: INSERT Propietario: SCOTT
Nombre de la tabla: DEPT Privilegio concedido: DELETE Propietario: SCOTT
Nombre de la tabla: EMP Privilegio concedido: UPDATE Propietario: SCOTT
Nombre de la tabla: EMP Privilegio concedido: SELECT Propietario: SCOTT
Nombre de la tabla: EMP Privilegio concedido: INSERT Propietario: SCOTT
Nombre de la tabla: EMP Privilegio concedido: DELETE Propietario: SCOTT

Procedimiento PL/SQL terminado correctamente.
```

   2. Realiza un procedimiento que reciba un nombre de usuario, un privilegio y un objeto y nos muestre el mensaje 'SI, DIRECTO' si el usuario tiene ese privilegio sobre objeto concedido directamente, 'SI, POR ROL' si el usuario lo tiene en alguno de los roles que tiene concedidos y un 'NO' si el usuario no tiene dicho privilegio.

```
create or replace procedure PrivilegiosUsuarios(p_usuario VARCHAR2,
                                      p_privilegio VARCHAR2,
                                      p_objeto VARCHAR2)
is
    v_cont number:=0;
begin
    select count(*) into v_cont 
    from dba_tab_privs
    where grantee=upper(p_usuario)
    and privilege=upper(p_privilegio)
    and table_name=upper(p_objeto);
    if v_cont=0 then
        PrivilegiosRol(p_usuario,p_privilegio,p_objeto);
    else
        dbms_output.put_line('SI, DIRECTO');
    end if;
end;
/

create or replace procedure PrivilegiosRol(p_usuario VARCHAR2,
                                            p_privilegio VARCHAR2,
                                            p_objeto VARCHAR2)
is
    v_cont  number:=0;
begin
    select count(*) into v_cont
    from dba_role_privs
    where grantee=upper(p_usuario)
    and granted_role in (select role
                        from role_tab_privs
                        where privilege=upper(p_privilegio)
                        and table_name=upper(p_objeto));
    if v_cont=0 then
        dbms_output.put_line('NO');
    else
        dbms_output.put_line('SI, POR ROL');
    end if;
end;
/
```

Pruebas de funcionamiento
```
SQL> exec PrivilegiosUsuarios('JUANDI','SELECT','TARIFAS');
SI, POR ROL

Procedimiento PL/SQL terminado correctamente.

SQL> exec PrivilegiosUsuarios('JUANDI','SELECT','DEPT');
SI, DIRECTO

Procedimiento PL/SQL terminado correctamente.

SQL> exec PrivilegiosUsuarios('JUANDI','SELECT','DEPTSÇ');
NO

Procedimiento PL/SQL terminado correctamente.
```