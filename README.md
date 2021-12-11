# API-SVG
API com Node e Express que permite consultar o path e o viewbox das geometrias dos municípios do banco do IBGE.

## Inicialização

No PostgreSQL é necessário, além da importação dos shapefiles referentes aos municípios, a criação da função que retorna o SVG de uma geometria:

```
CREATE OR REPLACE FUNCTION getViewBox(TEXT) RETURNS TEXT AS $$ 

	DECLARE 
             nome1 ALIAS FOR $1;
	     viewBox TEXT;
	     BEGIN
		  SELECT INTO viewBox CAST(ST_xmin(ST_Envelope(geom)) as varchar) || ' ' || 
                  CAST(ST_ymax(ST_Envelope(geom)) * -1 as varchar) || ' ' ||
                  CAST(ST_xmax(ST_Envelope(geom)) - ST_xmin(ST_Envelope(geom)) as varchar) || ' ' ||
                  CAST(ST_ymax(ST_Envelope(geom)) - ST_ymin(ST_Envelope(geom)) as varchar)
                  FROM municipio
                  WHERE nome ilike nome1;
	          return viewBox;
	END;
	
$$language plpgsql;
```

Para inicializar a API vocês deverão:
1. Clonar o repositório
2. Criar na pasta raiz um arquivo .env, que apresenta os parâmetros de configuração do banco e a porta que a API irá escutar

Exemplo do arquivo .env (trocar os valores das chaves pelos dados do seu banco):
```
DB_USER={usuário do postgres}
DB_PASSWORD={senha do postgres} 
DB_HOST={host do postgis no docker}
DB_PORT={porta do postgis no docker} 
DB_DATABASE={nome do banco - case sensitive}
PORT={porta que a API irá utilizar}
```

3. ```npm i```
4. ```npm start```

## Alterar parâmetros
Você pode alerar os dados da conexão do banco e da porta da API no arquivo .env

## Uso
Exemplo, para consultar o path e a viewBox da cidade de Apodi, considerando que a API está ouvindo na porta 3000:
```
http://localhost:3000/getSvg/apodi
http://localhost:3000/getViewBox/apodi
```
