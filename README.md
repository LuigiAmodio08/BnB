# BnB
import tkinter as tk
from tkinter import messagebox
from datetime import datetime


class BnB:
	def __init__(self):
	   
		self.stanze = {
			f"Stanza {i+1}": {"posti": 2, "prenotata": False} for i in range(10)
		}
		
		self.prezzo_bassa = 100
		self.prezzo_alta = 150
	  
		self.prenotazioni = []

   
	def calcola_prezzo(self, giorni, alta_stagione):
		if alta_stagione:
			prezzo = self.prezzo_alta
		else:
			prezzo = self.prezzo_bassa
		return prezzo * giorni

	
	def prenota_stanza(self, nome_stanza, nome_cliente, giorni, alta_stagione):
		if self.stanze[nome_stanza]["prenotata"]:
			return False
		
		costo = self.calcola_prezzo(giorni, alta_stagione)
		
		prenotazione = {
			"cliente": nome_cliente,
			"stanza": nome_stanza,
			"giorni": giorni,
			"stagione": "Alta Stagione" if alta_stagione else "Bassa Stagione",
			"costo": costo
		}
	 
		self.prenotazioni.append(prenotazione)
	   
		self.stanze[nome_stanza]["prenotata"] = True
		return True

	
	def annulla_prenotazione(self, nome_stanza):
		if self.stanze[nome_stanza]["prenotata"]:
			self.stanze[nome_stanza]["prenotata"] = False
		   
			self.prenotazioni = [p for p in self.prenotazioni if p["stanza"] != nome_stanza]
			return True
		return False


class BnBApp:
	def __init__(self, root, bnb):
		self.bnb = bnb
		self.root = root
		self.root.title("Gestione Prenotazioni BnB - Orsi Coraggiosi")
		self.root.geometry("800x600")
		self.root.configure(bg="#e0f7fa")


	def schermata_iniziale(self):
	   
		self.frame_iniziale = tk.Frame(self.root, bg="#e0f7fa")
		self.frame_iniziale.pack(fill="both", expand=True)

		
		self.label_titolo = tk.Label(self.frame_iniziale, text="Benvenuti a Orsi Coraggiosi", font=("Helvetica", 24, "bold"), bg="#e0f7fa", fg="#004d40")
		self.label_titolo.pack(pady=40)


		self.label_descrizione = tk.Label(self.frame_iniziale, text="Un'esperienza unica nella splendida Costiera Amalfitana.", font=("Helvetica", 16), bg="#e0f7fa", fg="#004d40")
		self.label_descrizione.pack(pady=20)

	   
		self.bottone_prenotazione = tk.Button(self.frame_iniziale, text="Vai alla Prenotazione", font=("Helvetica", 14), bg="#007BFF", fg="white", command=self.mostra_schermata_prenotazione)
		self.bottone_prenotazione.pack(pady=40, side="bottom", anchor="se", padx=20, )

	def mostra_schermata_prenotazione(self):
		self.frame_iniziale.destroy()
		self.crea_schermata_prenotazione()

	def crea_schermata_prenotazione(self):
 
		self.main_frame = tk.Frame(self.root, bg="#e0f7fa")
		self.main_frame.pack(padx=20, pady=20, fill="both", expand=True)

	  
		self.frame_input = tk.Frame(self.main_frame, bg="#ffffff", bd=2, relief="solid")
		self.frame_input.pack(padx=20, pady=20, fill="x", expand=True)


		self.crea_form_input()

	 
		self.bottone_prenota = tk.Button(self.main_frame, text="Prenota", font=("Helvetica", 14), bg="#007BFF", fg="white", command=self.prenota)
		self.bottone_prenota.pack(pady=20)

		self.label_prenotazioni = tk.Label(self.main_frame, text="Lista Prenotazioni:", font=("Helvetica", 14, "bold"), bg="#e0f7fa", fg="#004d40")
		self.label_prenotazioni.pack(pady=10)
		self.lista_prenotazioni = tk.Listbox(self.main_frame, font=("Helvetica", 12), width=60, height=10, bg="#ffffff", fg="#004d40")
		self.lista_prenotazioni.pack(pady=10)

		
		self.bottone_elimina = tk.Button(self.main_frame, text="Elimina Prenotazione", font=("Helvetica", 14), bg="#FF4D4D", fg="white", command=self.elimina_prenotazione)
		self.bottone_elimina.pack(pady=20)

	def crea_form_input(self):
		"""Crea i widget di input per il modulo di prenotazione."""
		self.label_nome = tk.Label(self.frame_input, text="Nome Cliente:", font=("Helvetica", 12), bg="#ffffff", fg="#004d40")
		self.label_nome.grid(row=0, column=0, padx=10, pady=10, sticky="w")
		self.entry_nome = tk.Entry(self.frame_input, font=("Helvetica", 12), width=30)
		self.entry_nome.grid(row=0, column=1, padx=10, pady=10)

		self.label_stanza = tk.Label(self.frame_input, text="Inserisci la stanza:", font=("Helvetica", 12), bg="#ffffff", fg="#004d40")
		self.label_stanza.grid(row=1, column=0, padx=10, pady=10, sticky="w")
		self.lista_stanze = tk.StringVar(value=list(self.bnb.stanze.keys())[0])
		self.lista_stanze_dropdown = tk.OptionMenu(self.frame_input, self.lista_stanze, *self.bnb.stanze.keys())
		self.lista_stanze_dropdown.config(font=("Helvetica", 12))
		self.lista_stanze_dropdown.grid(row=1, column=1, padx=10, pady=10)

		self.label_giorni = tk.Label(self.frame_input, text="Numero di giorni:", font=("Helvetica", 12), bg="#ffffff", fg="#004d40")
		self.label_giorni.grid(row=2, column=0, padx=10, pady=10, sticky="w")
		self.entry_giorni = tk.Entry(self.frame_input, font=("Helvetica", 12), width=30)
		self.entry_giorni.grid(row=2, column=1, padx=10, pady=10)

		self.label_stagione = tk.Label(self.frame_input, text="Seleziona la stagione:", font=("Helvetica", 12), bg="#ffffff", fg="#004d40")
		self.label_stagione.grid(row=3, column=0, padx=10, pady=10, sticky="w")
		self.stagione_var = tk.StringVar()
		self.stagione_var.set("Bassa Stagione")
		self.dropdown_stagione = tk.OptionMenu(self.frame_input, self.stagione_var, "Alta Stagione", "Bassa Stagione")
		self.dropdown_stagione.config(font=("Helvetica", 12))
		self.dropdown_stagione.grid(row=3, column=1, padx=10, pady=10)

	def prenota(self):
		nome_cliente = self.entry_nome.get()
		nome_stanza = self.lista_stanze.get()
		try:
			giorni = int(self.entry_giorni.get())
			stagione = self.stagione_var.get()
			if not nome_cliente:
				messagebox.showerror("Errore", "Inserisci il nome del cliente.")
				return
			if giorni <= 0:
				messagebox.showerror("Errore", "Il numero di giorni deve essere positivo.")
				return
			alta_stagione = True if stagione == "Alta Stagione" else False
			if self.bnb.prenota_stanza(nome_stanza, nome_cliente, giorni, alta_stagione):
				self.aggiungi_prenotazione_lista(nome_cliente, nome_stanza, giorni, alta_stagione)
				messagebox.showinfo("Prenotazione", f"Prenotazione effettuata con successo per {nome_stanza} da {nome_cliente}.")
			else:
				messagebox.showerror("Errore", f"La stanza {nome_stanza} è già prenotata.")
		except ValueError:
			messagebox.showerror("Errore", "Inserisci un numero valido per i giorni.")

	def aggiungi_prenotazione_lista(self, nome_cliente, nome_stanza, giorni, alta_stagione):
		stagione = "Alta Stagione" if alta_stagione else "Bassa Stagione"
		costo = self.bnb.calcola_prezzo(giorni, alta_stagione)
		prenotazione_str = f"{nome_cliente} - {nome_stanza} - {giorni} giorni - {stagione} - {costo} EUR"
		self.lista_prenotazioni.insert(tk.END, prenotazione_str)

	def elimina_prenotazione(self):
		selezione = self.lista_prenotazioni.curselection()
		if not selezione:
			messagebox.showerror("Errore", "Seleziona una prenotazione da eliminare.")
			return
		prenotazione_selezionata = self.lista_prenotazioni.get(selezione)
		nome_stanza = prenotazione_selezionata.split(" - ")[1]
		if self.bnb.annulla_prenotazione(nome_stanza):
			self.lista_prenotazioni.delete(selezione)
			messagebox.showinfo("Successo", f"Prenotazione per {nome_stanza} eliminata con successo.")
		else:
			messagebox.showerror("Errore", "Impossibile eliminare la prenotazione.")


if __name__ == "__main__":
	root = tk.Tk()
	bnb = BnB()
	app = BnBApp(root, bnb)
	root.mainloop()
