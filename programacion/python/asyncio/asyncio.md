https://docs.python.org/3/library/asyncio.html
https://pymotw.com/3/asyncio/
http://www.snarky.ca/how-the-heck-does-async-await-work-in-python-3-5
  este último es muy bueno. Tiene un ejemplo casi al final muy bueno para entender todo esto. Lo he comentado aún más en async_snarky_example.py
https://inv.vern.cc/watch?v=8aGhZQkoFbQ
  video de que es un event loop, es para JS, pero es bueno para entender el concepto
http://sahandsaba.com/understanding-asyncio-node-js-python-3-4.html
  entendiendo un event loop con python
http://latentflip.com/loupe
  ejemplo de como funciona un event loop de javascript a camara lenta
http://djangostars.com/blog/asynchronous-programming-in-python-asyncio/

curio.md  coroutine concurrency library. Más rápida y pequeña que asyncio. No extensible

Asynchronous I/O, event loop, coroutines and tasks. Muy orientado a conexiones.

Mucho más sencillo que usar threads.

Event loop, es la base de como funciona (es como funciona JS en los navegadores, o la programación GUI):
is a programming construct that waits for and dispatches events or messages in a program". Basically an event loop lets you go, "when A happens, do B

Think of async/await as an API for asynchronous programming. La idea es que se usen estas dos palabras para crear funciones y métodos para esperar, independientemente de que event loop estemos usando por debajo.
Se podría usar por debajo asyncio, curio, etc, pero las funciones seguirian siendo las mismas.

Async, en grandes rasgos, nos provee de un EventLoop, Tasks y sleep.
Nosotros programaremos nuestras corutinas (tasks), que correran en el EventLoop y llamarán de vez en cuando a sleep para esperar.


Las corutinas tienen esta forma:
async def main():
    await asyncio.sleep(1)
    print('hello')

asyncio.run(main()) # la ejecutamos con esto



Que podemos pasar a await()
  - otra corutina
  - alguna función que implemente __await__() que retorne un iterador
  - no aceptará un generador a no ser que implemente ser una corutina (con el decorador)


Asyncio + corutinas
Ejecutar código asíncrono de forma "secuencial".

Asyncio + as_completed
Ejecutar varias corutinas en paralelo e ir trabajando con los resultados de las que vayan terminando
    for coroutine in asyncio.as_completed(coroutines):
        url, wait_time = yield from coroutine
        print('Coroutine for {} is done'.format(url))




# Ejecutar tareas concurrentemente
https://docs.python.org/3/library/asyncio-task.html#running-tasks-concurrently

Asyncio + gather
Esperar a que todas las corutinas terminen para proseguir
    coroutines = [get_url(url) for url in ['URL1', 'URL2', 'URL3']]
    results = yield from asyncio.gather(*coroutines)
    print(results)

Poner varios "async def" a correr (esperar hasta que todos terminen).
Crearemos esas task con asyncio.create_task.
Lo típico será crear una queue y pasársela por parámetro.
async def worker(name, queue):
  while True:
    x = await queue.get()

queue = asyncio.Queue()
taskA = asyncio.create_task(worker('worker1', queue))
queue.put_nowait(añadimos el trabajo)
await asyncio.gather(tasksA(), taskB())




También podemos esperar hasta que uno termine, o salte una excepción o todos terminen:
done, pending = asyncio.wait(aws, *, loop=None, timeout=None, return_when=ALL_COMPLETED)¶

CUIDADO! parece que con FIRST_COMPLETED no nos aseguran solo devolvernos una en done
Podemos mirar si tenemos algo de lo queremos esperar en pending:
if ok_join in pending or error_join in pending:
  await asyncio.wait(pending, return_when=asyncio.FIRST_COMPLETED)




Si queremos esperar a la cola junto con otras tasks (sintaxis no probada, creo que es así):
q = asyncio.Queue()
taskA = asyncio.create_task(q.join())
await asyncio.gather(tasksA(), taskB(), q.join)

Creo que es mejor hacer un asyncio.gather de las tasks y el queue.join(), para evitar quedarnos colgados si se genera una excepción y por ello nunca se procesa la cola
La idea es que si solo esperamos al fin de la cola y una corutina ha fallado, la cola nunca se dará por finalizada.




# Loops (creo que esto es antiguo)
loop = asyncio.get_event_loop()

Añadir tareas:
t = loop.create_task(coro) # Si la añadimos asi, podemos correr el run_forever. Devuelve un Task, por si queremos usar run_until_complete

Opciones para correrlo:
loop.run_forever()
loop.run_until_complete(future) # El future sera una corutina tipo: "async def f()"

Chequeos / Gestion:
loop.is_running()
loop.is_closed()
loop.stop()

Al terminar:
loop.close()


Si queremos esperar a varias tasks a que terminen:
await asyncio.wait([on_con_lost, start_server])




# Scheduler
Podemos hacer un scheduler muy sencillo con:
AbstractEventLoop.call_later(delay, callback, *args)

loop = asyncio.get_event_loop()
loop.call_later(2, lambda: print("hola"))
loop.run_forever()

Pondrá "hola" al pasar dos segundos.

Tambien tenemos call_at para pasarle un tiempo absoluto.



# Futures, no entiendo aun para que se usan
AbstractEventLoop.create_future()
Create an asyncio.Future object attached to the loop.

This is a preferred way to create futures in asyncio




# A modo resumen
Obtenemos un objeto loop.
Sobre el montamos corutinas o funciones que están esperando algo (stdin, socket, etc).
Ponemos a correr el loop.

El loop, en cada vuelta, mira a ver si ya tiene los datos disponibles para las funciones que esperan algo. Si es así, las ejecuta.
Luego ejecuta las otras corutinas que tenga registradas.


# Async / Await
http://benno.id.au/blog/2015/05/25/await1

A partir de python3.5 para definir una corutina se hace:

async def funcion():
  pass

Para ejecutar esa corutina podemos llamar a await desde dentro de otra corutina:
async def otraf()
  c = funcion()
  await c

# Excepciones
http://sahandsaba.com/understanding-asyncio-node-js-python-3-4.html
"Handling Exceptions"

Podemos lanzar Excepciones a una corutina:
c = coroutine()
c.next()
c.throw(Exception("Have an exceptional day!"))


# Cancelar corutinas
https://docs.python.org/3/library/asyncio-dev.html#cancellation

Cuidado, una corutina (task) puede que ser cancelada. Cuidado con suponer que no va a ser cancelada al programar.



# Ejecutar corutina con un timeout
https://docs.python.org/3/library/asyncio-task.html#asyncio.wait_for

result = await asyncio.wait_for(fut, 60.0)

Si salta excepcion la podemos capturar con:
except asyncio.TimeoutError:


Para evitar que cuando salte el timeout cancelemos la corutina usamos shield:
https://docs.python.org/3/library/asyncio-task.html#asyncio.shield

res = await shield(something())

Quedaría (no probado):
result = await shield(asyncio.wait_for(fut, 60.0))




# Multithreading
https://docs.python.org/3/library/asyncio-dev.html#concurrency-and-multithreading

Si queremos un handler contra otro thread usar: loop.call_soon_threadsafe(callback, *args)

Cuidado si intentamos acceder a objetos de asyncio desde otro thread. No son thread safe. Si lo necesitamos, mejor interactuar via:
loop.call_soon_threadsafe(fut.cancel)

To handle signals and to execute subprocesses, the event loop must be run in the main thread.

Para meter una corutina en el event loop desde otro thread: future = asyncio.run_coroutine_threadsafe(coro_func(), loop)

AbstractEventLoop.run_in_executor() para ejecutar cosas fuera del event loop, en otro thread. Por ejemplo cosas que bloqueen (llamar a requests por ejemplo)


# Ejemplos
https://github.com/python/asyncio/tree/master/examples

Ejemplo de queue + workers
https://docs.python.org/3.8/library/asyncio-queue.html#example


# Debug
https://docs.python.org/3/library/asyncio-dev.html#debug-mode-of-asyncio
Aqui vienen una serie de cosas que chequear si estamos teniendo problemas

Dentro poner
import logging
logging.basicConfig(level=logging.DEBUG)

Ejecutar con:
PYTHONASYNCIODEBUG=1 python -Wdefault

Por defecto asyncio logea en INFO, si queremos cambiarlo:
logging.getLogger('asyncio').setLevel(logging.WARNING)



# Librerias para asyncio
https://github.com/aio-libs

Si queremos usar HTTP:
to do HTTP requests you either have to construct the HTTP request yourself by hand, use a project like the aiohttp framework (https://pypi.python.org/pypi/aiohttp) which adds HTTP on top of another event loop (in this case, asyncio), or hope more projects like the hyper library (https://pypi.python.org/pypi/hyper) continue to spring up to provide an abstraction for things like HTTP which allow you to use whatever I/O library you want (although unfortunately hyper only supports HTTP/2 at the moment).

Requests no se puede usar: the synchronous I/O that requests uses is baked into its design (https://github.com/kennethreitz/requests/issues/2801)

https://github.com/aio-libs/async-timeout
Para lanzar bloques de código con un timeout maximo
Puede sustituir a asyncio.wait_for()


https://aiomas.readthedocs.io/en/latest/
Paso de mensajes (RPC) para multi-systems


# Ejemplos
https://docs.python.org/3/library/asyncio-dev.html

asyncio.ensure_future(create())
asyncio.ensure_future(write())
asyncio.ensure_future(close())
await asyncio.sleep(1)

Llama a create, write y close de manera asíncrona, se van ejecutando en paralelo.


yield from asyncio.ensure_future(create())
yield from asyncio.ensure_future(write())
yield from asyncio.ensure_future(close())
await asyncio.sleep(1)

Se llama a create, write y close secuencialmente


Ejemplo de producer/consumer con asyncio.Queue
https://gist.github.com/akrylysov/ebab39ca9dafd292916e


# REPL
import asyncio
loop = asyncio.get_event_loop()
loop.run_until_complete(etcdcli.read("/prueba"))



# Tests
https://pypi.python.org/pypi/pytest-asyncio
https://stefan.sofa-rockers.org/2015/04/22/testing-coroutines/
https://stefan.sofa-rockers.org/2016/03/10/advanced-asyncio-testing/

pip install pytest pytest_asyncio
