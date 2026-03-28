import tkinter as tk
from collections import deque
import threading
import time
 
 
class ColaImpresion:
    def __init__(self):
        self.cola = deque()
        self.impresora_ocupada = False
 
        self.root = tk.Tk()
        self.root.title("Cola de impresión")
 
        tk.Label(self.root, text="Nombre del documento").pack()
        self.entry_nombre = tk.Entry(self.root)
        self.entry_nombre.pack()
 
        tk.Label(self.root, text="Número de páginas").pack()
        self.entry_paginas = tk.Entry(self.root)
        self.entry_paginas.pack()
 
        tk.Label(self.root, text="Tiempo por página (segundos)").pack()
        self.entry_tiempo_doc = tk.Entry(self.root)
        self.entry_tiempo_doc.pack()
 
        tk.Button(self.root, text="Agregar documento", command=self.agregar).pack()
        tk.Button(self.root, text="Iniciar impresión", command=self.iniciar_impresion).pack()
 
        self.label_estado = tk.Label(self.root, text="Impresora libre", fg="blue")
        self.label_estado.pack()
 
        self.root.mainloop()
 
    def agregar(self):
        nombre = self.entry_nombre.get()
        paginas = int(self.entry_paginas.get())
        tiempo_doc = float(self.entry_tiempo_doc.get())
        self.cola.append((nombre, paginas, tiempo_doc))
        self.label_estado.config(text=f"{nombre} agregado a la cola")
 
    def iniciar_impresion(self):
        hilo = threading.Thread(target=self.proceso_impresion)
        hilo.start()
 
    def proceso_impresion(self):
        if self.impresora_ocupada:
            return
        self.impresora_ocupada = True
        while self.cola:
            nombre, paginas, tiempo_doc = self.cola.popleft()
            self.actualizar_estado(f"Imprimiendo {nombre}")
            for i in range(1, paginas + 1):
                self.actualizar_estado(f"{nombre}: Página {i}/{paginas}")
                time.sleep(tiempo_doc)
            self.actualizar_estado(f"{nombre} terminado")
            time.sleep(1)
        self.actualizar_estado("No hay documentos en cola")
        self.impresora_ocupada = False
 
    def actualizar_estado(self, mensaje):
        self.label_estado.config(text=mensaje)
 
 
ColaImpresion()
