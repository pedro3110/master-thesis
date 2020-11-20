
Restricciones
	* en las funciones especiales, no se pueden utilizar dentro de la funcion simbolos +,-,*,/,etc. En
	lugar de eso, hay que crear una Aux que haga esa cuenta, y meterle como input esa variable a la funcion

Update:
	* agrego el atributo 'parent' a todos los atomicos (es necesario porque puede que tengamos el mismo atomico 
	- es decir, con el mismo nombre - en diferentes modulos, y de alguna forma hay que diferenciarlos)
	* empiezo a estructurar un archivo traductor.py para traducir tanto a devsml como a archivos .h y .cpp
	* el output de traductor.py por ahora va a lotka-volterra-nested/top.xml y los .h y .cpp van a ir a lotka-volterra-nested/atomics/*
	* agrego DEVSGenerator.py con el proposito de hacer lo que puse mas arriba
	* Nota : algo importante, es que las Cte's van a poder tener inputs! (!= a como estaba antes). Esto es basicamente por el tema de las
	funciones especiales que pueden haber en la 'equation' de la Cte

Pendientes:
	* reestructurar el codigo un toque... usar clases / subclases
	* terminar generador de CellDevs
	* DEVSPulse: 
		* cambiar 'start' por first_pulse


Comentarios:
	* DEVSPulse:
		. que pasa si en equation biene un PULSE(,,) + 'algo'? (guardar ultimo valor de PULSE) => comportamiento indefinido (ver que pasa en STELLA ... es medio raro)
		. no tiene sentido (y el traductor no lo soporta) que una funcion PULSE este en un Aux (solamente tiene sentido en un Flow, que si lo soportamos)


Instrucciones para crear un nueva funcion especial:

1. en 'root/traductor_xmile_devsml_cdpp.py':
	. modificar CPP_H_TEMPLATES_FILENAMES con el nombre del archivo del template correspondiente al nuevo componente (supongamos que se llama 'ComponenteX')
2. en 'root/templates':
	. agregar un .h y un .cpp llamados 'ComponenteX.h' y 'ComponenteX.cpp'
	. NOTA: posteriormente va a haber que modificar este template para que reciba los parametros correspondientes
3. en 'root/modulosDEVS/DEVSAtomic'
	. crear un modulo nuevo llamado DEVSComponenteX
4. en 'root/modulosAuxiliares/SpecialFunctionFinder.py'
	. importar el modulo creado en 3: from modulosDEVS.DEVSAtomic.DEVSComponenteX import DEVSComponenteX
	. en 'generateSpecialFunction' agregar la opción para la componente nueva
	. modificar self.regexs para poder parsear las expresiones correspondientes a la nueva función
5. en 'root/DEVSGenerator'
	. agregar una seccion que le pase los parametros correspondientes a una instancia de ComponenteX al template correspondiente
6. en modulosCDPP/HCPPAtomic/HCPAtomicGenerator.py agregar el nuevo atómico
7. agregar el template correspondiente en Traductor/config.py
8. Ejecutar una traducción y 
	a. mirar el archivo top.xml y verificar que se generó bien el nuevo componente con sus inputs/outputs/parametros/etc de forma correcta
	b. mirar los archivos .h y .cpp generados para dicha componente, asi como el .ma. Verificar que este todo ok
	c. Correr simulador.
	d. rezar (un poquito al menos)