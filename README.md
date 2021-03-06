
# %% Import av pakker:
    
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
import datetime


# %% Svar på del a:

file = "support_uke_24.xlsx"
data = pd.read_excel(file)     # excel fil leses inn

u_dag = data["Ukedag"].values      # array oprettes
kl_slett = data["Klokkeslett"].values
varighet = data["Varighet"].values
score = data["Tilfredshet"].values


# %% Svar på del b:
    
ant_man = np.count_nonzero(u_dag == "Mandag")# telling av antall henvendelser for mandag 
ant_tir = np.count_nonzero(u_dag == "Tirsdag") 
ant_ons = np.count_nonzero(u_dag == "Onsdag")
ant_tor = np.count_nonzero(u_dag == "Torsdag")
ant_fre = np.count_nonzero(u_dag == "Fredag")
#print(" del b)    Antall henvendelser mandag: ",ant_man)
#print(" del b)    Antall henvendelser tirsdag:",ant_tir)
#print(" del b)    Antall henvendelser onsdag: ",ant_ons)
#print(" del b)    Antall henvendelser torsdag:",ant_tor)
#print(" del b)    Antall henvendelser fredag: ",ant_fre)

plt.close("all")     # histogram lages
plt.hist(u_dag, bins = 5)      
plt.xlabel("Svar paa oppgave del b)")
plt.figure()
plt.show


# %% Svar på del c:

min_varighet = np.argmin(varighet)     # minste samtaletid finnes
max_varighet = np.argmax(varighet)
min_var = varighet[min_varighet]       # minste samtaletid hentes med index
max_var = varighet[max_varighet]
print(" del c)    Minste samtaletid (timer:minutter:sekunder)", min_var)
print(" del c)    Lengste samtaletid er", max_var) # leses til skjerm som "string" type


# %% Svar på del d:

def time(varighet):     # definerer hvordan tiden i arrayet varighet skal leses 
    hh, mm , ss = map(int, varighet.split(':'))
    return ss + 60*(mm + 60*hh)     # tiden regnes om til sekunder

tot_henv = ant_man + ant_tir + ant_ons + ant_tor + ant_fre # totale antall henvendelser

varighet_sekunder=[]    # liste som lagrer varighetene i sekunder og som integer

i = 0     #  loop som henter tall fra array varighet og lagrer i varighet_sekunder
while i < tot_henv:
    list_length = len(varighet_sekunder)
    varighet_sekunder.append(time(varighet[i]))  
    i = i + 1

def mean(l):      # utregning av gjennomsnitt samtaletid i sekunder
    avg = sum(l) / len(l) 
    return avg

average = mean(varighet_sekunder)
average_round = round(average)

average_min_sek = str( # gjør om fra integer sekunder til string minutter og sekunder
    datetime.timedelta(
        seconds=average_round)) # bruker modulen "datetime" til tids-manipulering 
  
print(" del d)    Gjennomsnittlig samtaletid er",
      average_min_sek)     # gjennomsnittstid skrives til skjerm 


# %% Svar på del e:

def time(kl_slett):     # definerer hvordan tiden skal skrives ut
    hh, mm , ss = map(int, kl_slett.split(":"))
    return hh     # tiden leses inn som hh,mm,ss og skrives ut som hh

kl_slett_list = []     # liste hvor hele timer/integer lagres

i = 0     # loop som henter tall fra array kl_slett og lagrer i kl_slett_liste
while i < tot_henv:
    list_length = len(kl_slett_list)
    kl_slett_list.append(time(kl_slett[i]))  
    i = i + 1
    
kl1 = 0      # variabel for antall henvendelser mellom 08:00 og 10:00
kl2 = 0      # variabel for antall henvendelser mellom 10:00 og 12:00
kl3 = 0      # variabel for antall henvendelser mellom 12:00 og 14:00
kl4 = 0      # variabel for antall henvendelser mellom 14:00 og 16:00
    
for i in range(len(kl_slett_list)):     # loop som teller antall henvendelser
    if kl_slett_list[i] < 10 and kl_slett_list[i] >= 8:
        kl1 += 1
    elif kl_slett_list[i] < 12 and kl_slett_list[i] >= 10:
        kl2 += 1
    elif kl_slett_list[i] < 14 and kl_slett_list[i] >= 12:
        kl3 += 1
    elif kl_slett_list[i] < 16 and kl_slett_list[i] >= 14:
        kl4 += 1

labels = "kl: 08-10", "kl: 10-12", "kl: 12-14", "kl: 14-16"     # plot info
antall = kl1, kl2, kl3, kl4     # antall henvendelser lagres som tuple i "antall"
sizes = [kl1, kl2, kl3, kl4]    # størrelse paa kakestykkene lagres som integer i liste
colors = ["gold", "yellowgreen", "lightcoral", "lightskyblue"]
explode = (0.1, 0, 0, 0)  

plt.pie(sizes, explode=explode, labels=labels, colors=colors,
autopct='%1.1f%%', shadow=True, startangle=110)
plt.xlabel("Svar paa oppgave del e)")
plt.legend(antall, loc="best")
plt.show()


# %% Svar på del f:

negativ = 0     # antall negative stemmer fra kunder
positiv = 0     # antall positive stemmer fra kunder
noytral = 0     # antall noytrale stemmer fra kunder

for i in range (len(score)):     # loop som teller antall stemmer utefra score
    if score[i] < 7:
        negativ += 1
    elif score[i] > 8:
        positiv += 1
    elif score[i] >= 7 and score[i] <= 8:
        noytral += 1

tot = negativ + positiv + noytral     # totale antall stemmer lagres i variabelen "tot"

nps = (positiv/tot - negativ/tot) *100     # NPS regnes ut 

nps_round = [round(nps)]     # lager liste av avrundet nps verdi

for i in range(len(nps_round)):     # loop for kommentar til NPS score  
    if nps_round[i] <= 0:           # NPS score og kommentar skrives til skjerm
        print(" del f)    Dette var ikke så bra. MORSE supportavdeling har fått en NPS på",
              nps_round[0])
    elif nps_round[i] > 0 and nps_round[i] <= 50:
        print(" del f)    Dette var bra  \N{grinning face}  MORSE supportavdeling har fått en NPS på",
              nps_round[0])
    elif nps_round[i] > 50 and nps_round[i] <= 70:
        print(" del f)    Dette var veldig bra. MORSE supportavdeling har fått en NPS på",
              nps_round[0])
    elif nps_round[i] > 70: # skriver ut tallet med indeks [0] for å skjule firkantklammer i utskrift
        print(" del f)    Dette er høyeste score. MORSE supportavdeling har fått en NPS på",
              nps_round[0])


