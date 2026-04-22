import numpy as np
import matplotlib.pyplot as plt

class Capteur:
    def __init__(self, biais, bruit):
        self.biais = biais
        self.bruit = bruit

    def donner_mesure(self, temperature_reelle):
        erreur_aleatoire = np.random.normal(0, self.bruit)
        return temperature_reelle + self.biais + erreur_aleatoire

class Correcteur(Capteur):
    def __init__(self, biais, bruit):
        super().__init__(biais, bruit)
        self.correction = 0

    def calibrer(self):
        m0 = self.donner_mesure(0)
        m100 = self.donner_mesure(100)
        
        self.correction = ((m0 - 0) + (m100 - 100)) / 2
        print(f"Correction = {self.correction:.3f}")

    def lire_propre(self, temp_reelle):
        brute = self.donner_mesure(temp_reelle)
        return brute - self.correction

# Simulation
mon_systeme = Correcteur(biais=1.8, bruit=0.4)
mon_systeme.calibrer()

temps_reels = np.linspace(10, 50, 60)

brutes = [mon_systeme.donner_mesure(t) for t in temps_reels]
propres = [mon_systeme.lire_propre(t) for t in temps_reels]

err_avant = np.mean(np.abs(np.array(brutes) - temps_reels))
err_apres = np.mean(np.abs(np.array(propres) - temps_reels))

print(f"Erreur avant: {err_avant:.3f}")
print(f"Erreur après: {err_apres:.3f}")
