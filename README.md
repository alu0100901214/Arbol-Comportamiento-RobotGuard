# Arbol Comportamiento RobotGuard
## Hacer el que el NPC vuelva a la última posición donde vio al jugador antes de huir. Debe volver después de que su salud se haya recuperado por encima de 40.

Para realizar esta tarea, he creado 4 Tasks dentro de AI.cs y un nuevo árbol en BotAI.BT.txt

primero "SaveLastPlayerPosition" guarda la posición del jugador en el momento en el que hace el NPC hace "Flee":

```
    [Task]
    public void SaveLastPlayerPosition() {
        lastPlayerPos = player.position;
        Task.current.Succeed();
    }
```

La segunda Task "SetLastPlayerDestination" selecciona como destino la posición guardada en caso de que se haya memorizado la posición del jugador al hacer un "Flee":

```
    [Task]
    public void SetLastPlayerDestination() {
        if(knowPlayerPos)
            agent.SetDestination(lastPlayerPos);
        Task.current.Succeed();
    }
```

La tercera Task "HasLastPlayerDestination" comprueba si se guardo la posición del jugador en un Flee:

```
    [Task]
    public bool HasLastPlayerDestination() {
        if(knowPlayerPos)
            return true;
        else return false;
    }
```

Y la cuarta Task pone a false la variable booleana knowPlayerPos una vez acabada la busqueda del jugador:

```
    [Task] 
    public void UnSetLastPlayerDestination(){
        knowPlayerPos = false;
        Task.current.Succeed();
    }
```

Luego, en el árbol de comportamiento añado "SaveLastPlayerPosition" dentro de "Flee" en el momento en el que decide huir del jugador:

```
tree("Flee")
	sequence
		while IsHealthLessThan(40.0)
			while InDanger(50.0)
				sequence
					SaveLastPlayerPosition
					TakeCover
					MoveToDestination
```

Creo un nuevo árbol de comportamiento llamado "LookPlayer" que comprueba en un while si se guardo la ultima posición del jugador despues de un "Flee", si es asi, se dirige a dicha posición y al final se vuelve la variable booleana knowPlayerPos a false.

´´´
tree("LookPlayer")
	while HasLastPlayerDestination
		sequence
			SetLastPlayerDestination
			MoveToDestination
			UnSetLastPlayerDestination
´´´

Este árbol se añade en Patrol despues de comprobar que la vida no es menor que 40:

```
tree("Patrol")
	fallback
		tree("Flee")
		while not IsHealthLessThan(40.0)
			fallback
				tree("LookPlayer")
				tree("Attack")
				tree("LookAround")
				tree("Wander")
```

GIF con un ejemplo de la ejecución:

![gif](./GIF/arbolComportamiento.gif)
