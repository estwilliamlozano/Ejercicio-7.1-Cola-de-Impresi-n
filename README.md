import tkinter as tk
from collections import deque
import threading
import time

cola = deque()
impresora_ocupada = False


def agregar():
    nombre = entry_nombre.get()
    paginas = int(entry_paginas.get())
    tiempo_doc = float(entry_tiempo_doc.get())

    cola.append((nombre, paginas, tiempo_doc))
    label_estado.config(text=f"{nombre} agregado a la cola")

def iniciar_impresion():
    hilo = threading.Thread(target=proceso_impresion)
    hilo.start()

def proceso_impresion():
    global impresora_ocupada

    if impresora_ocupada:
        return

    impresora_ocupada = True

    while cola:
        nombre, paginas, tiempo_doc = cola.popleft()
        actualizar_estado(f"Imprimiendo {nombre}")

        for i in range(1, paginas + 1):
            actualizar_estado(f"{nombre}: Página {i}/{paginas}")
            time.sleep(tiempo_doc)

        actualizar_estado(f"{nombre} terminado")
        time.sleep(1)

    actualizar_estado("No hay documentos en cola")
    impresora_ocupada = False

def actualizar_estado(mensaje):
    label_estado.config(text=mensaje)


root = tk.Tk()
root.title("Cola de impresión")

tk.Label(root, text="Nombre del documento").pack()
entry_nombre = tk.Entry(root)
entry_nombre.pack()

tk.Label(root, text="Número de páginas").pack()
entry_paginas = tk.Entry(root)
entry_paginas.pack()

tk.Label(root, text="Tiempo por página (segundos)").pack()
entry_tiempo_doc = tk.Entry(root)
entry_tiempo_doc.pack()

tk.Button(root, text="Agregar documento", command=agregar).pack()
tk.Button(root, text="Iniciar impresión", command=iniciar_impresion).pack()

label_estado = tk.Label(root, text="Impresora libre", fg="blue")
label_estado.pack()

root.mainloop()
