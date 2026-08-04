
===== RUN 2026-08-04 21:15:24 UTC =====
Model: deepseek | Phase: RECON | Target: NONE

[0m
> build · deepseek-v4-flash-free
[0m
[0m$ [0mdig +short fyers.in A; echo "---MX---"; dig +short fyers.in MX; echo "---TXT---"; dig +short fyers.in TXT; echo "---NS---"; dig +short fyers.in NS
104.18.5.135
104.18.4.135
---MX---
10 mx.zoho.in.
20 mx2.zoho.in.
50 mx3.zoho.in.
---TXT---
"apple-domain-verification=e0dZzdVsUq95kw6Y"
"cursor-domain-verification-cf7a6k=LYMyrmRPaqN9pvfW7HNN56ixx"
"detectify-verification=1a1aa4b55942bf3209368b18bf23eb6f"
"figma-domain-verification=de1fa1af744ffe2fbd1813cce48fe58def2586fb4e7bef3aac047739e1b75524-1730780071"
"google-site-verification=M1Hhmd6JoUcjr6ig2WroV-3C7_PWq-StP627sxFJQNw"
"google-site-verification=ypw5QGdNwI8BPo3JUiZZigpiJp8D4xa4b1p3mko6K7s"
"openai-domain-verification=dv-4G51DvHGqLaGNqVIHCaSrwzZ"
"v=spf1 include:zoho.in include:transmail.net.in include:zcsend.in ~all"
---NS---
leia.ns.cloudflare.com.
bayan.ns.cloudflare.com.
[0m
I'll begin RECON on fyers.in, gathering read-only intelligence from public sources.
[0m$ [0mcurl -s "https://crt.sh/?q=%25.fyers.in&output=json" -m 60 | python3 -c "import sys,json; d=json.load(sys.stdin); names=set(); [names.add(n) for e in d for n in e.get('name_value','').split('\n')]; print('\n'.join(sorted(names)))" 2>/dev/null | sort -u
(no output)
[0m
Cloudflare-fronted, Zoho mail (MX/TXT), Detectify scanning. crt.sh returned empty; retrying and probing known assets.
[0m% [0mWebFetch https://fyers.in/
[0m
[0m$ [0mcurl -s "https://crt.sh/?q=fyers.in&output=json" -m 90 -H "Accept: application/json" -o /tmp/opencode/crt.json; wc -c /tmp/opencode/crt.json; python3 -c "
import json
try:
    d=json.load(open('/tmp/opencode/crt.json'))
    names=set()
    for e in d:
        for n in e.get('name_value','').split('\n'):
            names.add(n.strip().lower())
    for n in sorted(names): print(n)
except Exception as ex:
    print('ERR', ex)
"
150 /tmp/opencode/crt.json
ERR Expecting value: line 1 column 1 (char 0)
[0m
Cloudflare-fronted, Zoho MX, Detectify scanning, React marketing site. Key contacts found: `security@fyers.in`, `techsupport@fyers.in`. There's a Bug Bounty Program link. Retrying crt.sh and probing FYERS API subdomains.
[0m$ [0mfor h in api myapi trade dashboard chart www app web portal auth wwwapi data smartapi oauth my login myaccount mobile services api-docs api2 prod test beta openapi; do ip=$(dig +short ${h}.fyers.in A | head -1); if [ -n "$ip" ]; then echo "$h.fyers.in -> $ip"; fi; done; echo "== done =="
api.fyers.in -> data-fy-in-lb-1496158902.ap-south-1.elb.amazonaws.com.
myapi.fyers.in -> 104.18.4.135
trade.fyers.in -> 104.18.4.135
www.fyers.in -> 104.18.5.135
app.fyers.in -> 104.18.5.135
data.fyers.in -> 104.18.4.135
login.fyers.in -> 104.18.5.135
myaccount.fyers.in -> 104.18.4.135
api-docs.fyers.in -> 104.18.4.135
== done ==
[0m
[0m$ [0msleep 3; curl -s "https://crt.sh/?q=%25.fyers.in&output=json" -m 90 -o /tmp/opencode/crt2.json; wc -c /tmp/opencode/crt2.json | tr -d '\n'; echo; python3 -c "
import json
raw=open('/tmp/opencode/crt2.json','rb').read()
s=raw.decode('utf-8','ignore')
i=s.find('[')
d=json.loads(s[i:])
names=set()
for e in d:
    for n in e.get('name_value','').split('\n'):
        names.add(n.strip().lower())
for n in sorted(names): print(n)
" 2>&1 | head -100
150 /tmp/opencode/crt2.json
Traceback (most recent call last):
  File "<string>", line 6, in <module>
  File "/usr/lib/python3.12/json/__init__.py", line 346, in loads
    return _default_decoder.decode(s)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/lib/python3.12/json/decoder.py", line 337, in decode
    obj, end = self.raw_decode(s, idx=_w(s, 0).end())
               ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/lib/python3.12/json/decoder.py", line 355, in raw_decode
    raise JSONDecodeError("Expecting value", s, err.value) from None
json.decoder.JSONDecodeError: Expecting value: line 2 column 1 (char 1)
[0m
`api.fyers.in` resolves to an AWS ALB (ap-south-1) — the real API origin, while most web surfaces sit behind Cloudflare. Pulling more CT-log data via certspotter and locating the bug bounty policy.
[0m✗ [0mWebFetch https://fyers.in/trust-security/ failed
[91m[1mError: [0mStatusCode: non 2xx status code (404 GET https://fyers.in/trust-security/)
[0m
[0m$ [0mcurl -s "https://api.certspotter.com/v1/issuances?domain=fyers.in&include_subdomains=true&expand=dns_names" -m 60 -o /tmp/opencode/cs.json; python3 -c "
import json
try:
    d=json.load(open('/tmp/opencode/cs.json'))
    names=set()
    for e in d:
        for n in e.get('dns_names',[]):
            names.add(n.strip().lower().rstrip('.'))
    for n in sorted(names): print(n)
    print('TOTAL', len(names))
except Exception as ex: print('ERR', ex)
"
*.fyers.in
academy.egeospace.in
academy.getsecure360.com
academy.invictalearning.com
acme.evokesharedservices.com
agrud-support.wetranscloud.in
alms.conorg.live
apply.goajazzacademy.com
asap-test.ringlogix.com
assist.acecloudhosting.com
assist.amproindia.com
assist.applexus.com
assist.bizacuity.com
assist.cascadeheadway.com
assist.cienterprises.org.in
assist.dentistryautomation.com
assist.fyers.in
assist.itcare.in
assist.jasya.in
assist.kwalee.com
assist.metroride.com
assist.mindsprint.digital
assist.ofi.com
assist.pentacle.in
assist.rishonn.com
assist.rmbassociates.in
assist.talentformula.co
assist.technodoc.in
assist.techpase.in
assist.thehindu.co.in
assist.vijaycopolymers.com
assist.zylu.co
baatein.nieit.rf.gd
banyantree.kraftpixel.com
bigfish.spacematrix.com
business-support.truecaller.com
calendar.logisy.tech
campsupport.isportz.co
care.adda247.com
care.analytix.com
care.auditimeasia.com
care.tarasri.in
career.blend-ed.com
career.kshema.co
career.prepscale.com
career.qure.ai
career.reveriext.com
career.socialbeat.in
careers.a-bits.com
careers.addwebsolution.com
careers.aletasoftwarelabs.in
careers.allendigital.in
careers.aqmtechnologies.com
careers.bbinsight.com
careers.bbsglobal.in
careers.bhagirathtechnologies.com
careers.cayian.in
careers.cometchat.com
careers.cosmoops.com
careers.deeceeline.com
careers.eshiasolutions.com
careers.exotto.com
careers.facctum.com
careers.findernest.com
careers.finlyhq.com
careers.gnapitech.com
careers.godspeedxp.com
careers.gsasindia.com
careers.ignatiuz.com
careers.iide.co
careers.ijbridge.com
careers.indra.co.in
careers.initialyze.com
careers.iqlab.global
careers.jewult.com
careers.kmccorp.in
careers.knorish.com
careers.kvpcorp.com
careers.lendahandindia.org
careers.magicdecor.in
careers.microsave.net
careers.mobedoconsulting.com
careers.nurseera.com
careers.obenelectric.com
careers.onefinops.com
careers.paladintech.ai
careers.pragyashal.com
careers.remitap.com
careers.reraconsultants.in
careers.securin.io
careers.selah.net
careers.signellent.com
careers.sumadhuragroup.com
careers.trishlacornindia.com
careers.trisimglobal.com
careers.unborefoods.com
careers.vertoz.com
careers.websidezone.com
careers.xscad.com
careers.yellow.ai
careers.zero-pixels.com
careers.zodopt.com
cep.dkube.io
chambers.metalegal.in
chat.ajxtechnologies.com
chat.atmalubricants.com
chat.avinyainfotech.com
chat.devdensolutions.com
chat.editordesign.in
chat.rxdel.in
chat.skylink.net.in
chat.venpep.com
chat.ventureboost.ai
chat.zecomy.com
cliq.arikecare.org
cliq.auzin.com.au
cliq.bytebash.tech
cliq.chetasee.com
cliq.downtownrealtyj.com
cliq.ennuviz.com
cliq.fyers.in
cliq.kovidbioanalytics.com
cliq.ohul.in
cliq.rishonn.com
cliq.seccom.in
cmibhelpdesk.icai.org
cms.systanetworks.com
cohort2025.everychildcountsscaleup.org
cohorts.everychildcountsscaleup.org
collab.imbspectral.online
collab.intervaledu.com
community.orgevo.in
connect.aciesglobal.com
connect.ahambrahmaasmi.org
connect.athloft.com
connect.baskethunt.com
connect.boldandbeyond.in
connect.catalyst-partners.in
connect.celestialsys.com
connect.cfrce.org
connect.coworx.in
connect.cs.zohohost.in
connect.deskriver.com
connect.empfly.com
connect.fyers.in
connect.icsit.in
connect.irastrive.com
connect.kgep.in
connect.myacies.com
connect.nevrio.tech
connect.ojasimmigration.com
connect.omysha.org
connect.orientbell.com
connect.vemasolar.com
connect.wnygrp.com
connect.zustpe.com
connectindia.productspace.com
contosso.evokesharedservices.com
cs.allcloud.in
custassist.eterniawindows.com
dalmia.airditsoftware.com
data.carrierwheels.com
delivery.40plusshag.com
delivery.69matchclub.com
delivery.69nextdoor.com
delivery.abcd.adityabirlacapital.org
delivery.abcdigi.adityabirlacapital.org
delivery.abcdigital.adityabirlacapital.org
delivery.aia.com.ph
delivery.bumeran.com
delivery.ccmail.bombayshavingcompany.com
delivery.digital.adityabirlacapital.org
delivery.digitalabcd.adityabirlacapital.org
delivery.e-invoice.toplo.bg
delivery.e.whatnowatlanta.com
delivery.email.abandonedspaces.com
delivery.email.allhiphop.com
delivery.email.bluecarbonconsulting.com
delivery.email.cdllife.com
delivery.email.cgmagonline.com
delivery.email.deepstatewatch.com
delivery.email.dustyoldthing.com
delivery.email.face2faceafrica.com
delivery.email.faithhub.net
delivery.email.flightsearchdirect.com
delivery.email.freedomheadlines.com
delivery.email.higherperspectives.com
delivery.email.ibtmail.com
delivery.email.manmadediy.com
delivery.email.patriotdailypress.org
delivery.email.soaphub.com
delivery.email.tataneu.com
delivery.email.thevintagenews.com
delivery.email.warhistoryonline.com
delivery.emaildojo.io
delivery.engage.apnapaisa.com
delivery.erekrut.com
delivery.freshpikonline.com
delivery.funnel.perfectpay.com.br
delivery.futuregenerali.in
delivery.fyers.in
delivery.greatgather.com
delivery.housing.com
delivery.icicipruamc.com
delivery.indiamart.com
delivery.jeeng.brobible.com
delivery.karmaexperience.com
delivery.kfintech.com
delivery.klayschools.in
delivery.kmct.org
delivery.localdesire.com
delivery.lustycanadians.com
delivery.mail.apnapaisa.com
delivery.mail.perfectpay.com.br
delivery.mailer.indialends.com
delivery.nec.edu
delivery.newsletter.forbes.com
delivery.onlybangcity.com
delivery.onlydirtylocals.com
delivery.onlyfuckbook.com
delivery.ozziematch69.com
delivery.perfectpay.com.br
delivery.ppm.n3connect.com
delivery.privatelocals.com
delivery.quickride.in
delivery.realnex.com
delivery.regionaffair.com
delivery.reliancegeneralinsurance.co.in
delivery.rpy.club
delivery.sav.money
delivery.teachingchannel.com
delivery.trans.kotak811.com
delivery.visitlecce.eu
delivery.wildneighbours.com
desk.cs.zohohost.in
desk.gantavya.co
desk.multigb.com
desk.rmbassociates.in
desk.sana.insure
desk.satechdigital.com
desk.speedfreak.shop
deskin1.thandora.com
deskin2.thandora.com
deskin3.thandora.com
deskin4.thandora.com
deskin5.thandora.com
desl.samr360.com
devops.experiontechnologies.com
digikins.digikentro.com
egeocloud.academy.egeospace.in
email.arcamax.com
engage.akadia.io
er.fly91.in
ess.growith.io
etcad.samr360.com
faq.5paisaloans.com
fchrms.figcube.in
finance.realshine.xyz
form.divorcelawyer.in
form.lawyeradvise.org
form.manastha.com
form.nfadioffical.com
form.salarybox.in
formblr.lahl.in
forms.adarshsharma.in
forms.anilentp.com
forms.arbomart.com
forms.arthmitragurukulam.in
forms.bsmindia.com
forms.camonk.com
forms.capabl.in
forms.crafting-dialogue.com
forms.crewscale.com
forms.dasarimemorial.com
forms.dentalkart.com
forms.dhuniworldwide.com
forms.diabexy.com
forms.dnyx.in
forms.eduledge.in
forms.explerainc.com
forms.finpire.in
forms.finshieldadvisors.com
forms.firststepjob.com
forms.flexingit.com
forms.fyers.in
forms.himanshugyan.com
forms.iaapi.org
forms.iamironlady.com
forms.indiaitaly.com
forms.influence-alchemy.com
forms.innovationnest.in
forms.jaivel.aero
forms.jodhpurbreathbank.com
forms.jpscws.com
forms.kingshealth.in
forms.kkca.co.in
forms.kmvspaces.com
forms.limedigitalinstitute.org
forms.limitless.net.in
forms.lunalogo.com
forms.moneycompound.in
forms.novaex.ai
forms.oderp.in
forms.ojasimmigration.com
forms.pixelparadise.in
forms.pquations.com
forms.propsquares.com
forms.raaz.app
forms.saachijapan.com
forms.smiththerm.com
forms.sonvanecollege.com
forms.streetgooser.com
forms.subcom.tech
forms.suprams.net
forms.tamizhagroup.com
forms.taxaj.com
forms.techparrot.co
forms.techture.global
forms.theleadershipfederation.com
forms.tpstech.in
forms.trailblazersindia.com
forms.vergecloud.com
forms.vrmvk.in
forms.wealthcrafts.in
forms.winglet.in
forms.wrkmen.in
forms.yosnapps.com
forms.youngminds.app
forms.zecomy.com
forms.zibcha.com
foundry-apc-1.status.palantir.com
fulltimesilver.homeninjas.in
fyers.in
gigs.arecapot.in
hello.quanta.cloud
hello.quantazone.com
help.b2sell.com
help.bayfolio.com
help.evangel.stream
help.indiainfoline.com
help.lostfoundlink.com
help.mailerswift.com
help.mega.tc
help.novabenefits.com
help.phonon.io
help.pod.ai
help.qikink.com
help.qltech.com.au
help.qube.eco
help.recruit.poocho.co
help.servercake.in
help.signflexi.com
help.singathala.in
help.thanks.com
help.thinkdigits.co.in
help.usystechnology.com
help.veltmtours.com
help.woodshala.com
help.yunolearning.com
helpcenter.zee5.com
helpdesk.aniktantra.com
helpdesk.callcenterhosting.com
helpdesk.codehost.in
helpdesk.exalenze.com
helpdesk.magitt.com
helpdesk.moneymagnets.co.in
helpdesk.myhealthcare.life
helpdesk.tectoro.com
helpdesk.therealpbx.co.uk
helpstation.eterniawindows.com
hr.cloudcraftz.com
hr.dharmah.in
hr.fraxus3d.com
hr.grasko.com
hr.neuailes.io
hr.onebyzero.ai
hr.satvisolutions.com
hr.storypick.com
hr.supremeispat.com
hr.whiteblink.com
hrconnect.keva.co.in
hrm.matrixhive.com
hrm.visualyze.ai
hrms.1channel.ai
hrms.abhijayit.com
hrms.ariqt.com
hrms.cloudsparkle.in
hrms.cognetrylabs.com
hrms.credin.in
hrms.csinfocomm.com
hrms.enhisecure.com
hrms.gttourism.in
hrms.heimatverse.com
hrms.hueframe.com
hrms.kmccorp.in
hrms.lexingtonsoft.com
hrms.mesprosoft.com
hrms.pixelotech.com
hrms.ptc-in.com
hrms.quasarix.in
hrms.renewnowsolutions.com
hrms.sellergize.com
hrms.skylarks.co.in
hrms.technopark.org
hrms.technoproindia.com
hrms.trisimglobal.com
hrms.trojanhuntindia.com
hub.wolviosolutions.com
icall.synergymarine.in
in1.projectscd.com
in2.projectscd.com
incometax.dsquarepro.in
incommonsupport.certinext.io
indiashelter.sana.insure
info.practiceguru.pro
inout.cawalagyan.com
instantpayout.fyers.in
insync.infobeans.com
intranet.alternative-path.com
intranet.securin.io
intranet.thoughtlogik.com
it-helpdesk.corestack.io
it-support.ingenia.ai
job.cloudare.in
jobs.advenzone.com
jobs.auvidigital.com
jobs.echovme.in
jobs.hyggexworld.com
jobs.ivalueindia.com
jobs.la-mirage.in
jobs.liaplus.com
jobs.mindpooltech.com
jobs.ongrid.design
jobs.qenomy.com
jobs.scgbsolutions.com
jobs.spardhaonline.com
jobs.stratadigm.co
jobs.taapsomeone.com
jobs.techqunbasolutions.com
jobs.unmoat.com
jobs.yunica.co.in
join-us.elegrow.com
join.careerspotters.in
join.himanshugyan.com
join.indifoss.com
join.oneclicktechnologies.us
kb.ojasimmigration.com
kb.peerxp.com
kb.rioe.business
kb.trackgpsfleet.com
know.ingermany.in
learn.aarogyabharat.care
learn.adaptiveaiventures.com
learn.ancorlabs.org
learn.ascentbusiness.com
learn.asrsbot.com
learn.avinyainfotech.com
learn.cadfem.in
learn.cadopt.com
learn.careerspotters.in
learn.davidprotocol.com
learn.egs.co.in
learn.embarkai.in
learn.ennuviz.com
learn.enthu.com
learn.fyers.in
learn.gantavya.co
learn.hexallt.com
learn.hionstudios.com
learn.hornbilllabs.com
learn.instacharter.app
learn.instient.ai
learn.magiconsult.com
learn.marslabintel.com
learn.netconnectglobal.com
learn.nexgencompany.ai
learn.nsrcel.org
learn.prasads.co.in
learn.rareminds.in
learn.rkmm.org
learn.smiledu.in
learn.toppershill.com
learn.webistainfotech.com
learn.zenius.co
learning.avinyainfotech.com
live.incrito.com
lms.conorg.live
login.digitviral.com
m1.olacabs.com
mahagram.mahavastu.com
manage.emegh.com
marketdata-api-instaoptions.fyers.in
may182026.zoholearnqa.co.in
meet.ashprotech.com
meet.avisoltechnologies.com
meet.birse.io
meet.citizenforce.in
meet.classpep.com
meet.educationvillamusic.com
meet.entreverse.in
meet.futura.limited
meet.hyrrokkin.net
meet.ingsol.in
meet.nativewit.in
meet.palastree.com
meet.reallist.io
meet.sendinbox.io
meet.skylink.net.in
meet.sparkcliks.com
meet.subcom.tech
meet.synngular.com
meet.urva.co
meet.wingsunschool.com
meet.zelkincorp.com
meeting.abcchemistry.com
meeting.adsmunch.in
meeting.advenzone.com
meeting.aeonmed.in
meeting.amalan.in
meeting.appcronix.in
meeting.bhojpur-consulting.com
meeting.bizsetups.com
meeting.cikion.com
meeting.citronscientific.com
meeting.cspkart.com
meeting.devdensolutions.com
meeting.digitalaccountingclub.com
meeting.dreamskillcatcher.in
meeting.fireblazeaischool.in
meeting.fyers.in
meeting.galliustech.com
meeting.gemeriahair.in
meeting.globaldatafeeds.in
meeting.hbinfotech.com
meeting.ikeworld.co.in
meeting.infotroop.co.in
meeting.investstreet.in
meeting.josh99.in
meeting.kitlimited.com
meeting.kripaitsolutions.com
meeting.lamirage.co
meeting.marvelquant.com
meeting.naacenergy.co.in
meeting.naviorajourneys.com
meeting.nodespay.site
meeting.nrithyoham.in
meeting.omnisage.ai
meeting.rackwave.io
meeting.railsimulation.com
meeting.rexmious.com
meeting.ripplewalk.com
meeting.saiapex.in
meeting.serverwalainfranet.com
meeting.slashcurate.com
meeting.talkingdistance.com
meeting.technoevangelist.in
meeting.thepedagogycommunity.org
meeting.thrivetec.com
meeting.triumph-rcm.com
meeting.unitedmedical.in
meeting.velomec.com
meeting.virturesolve360.com
meeting.vorro.net
meeting.yunica.co.in
meeting.zaque.ai
meeting.zenzys.com
meetings.divineeac.com
meetings.goscience.in
meetings.scienceandhumans.com
meetings.testeasylab.com
membership.adif.in
mentorship.contractsandgrantsonline.com
messenger.mathaonline.com
mfsupport.indiainfoline.com
mnl.status.cloudsigma.com
mssupport.avertech.in
my.zineiq.com
myaccess.biogreenware.co.in
myhr.highondrive.com
myhr.natlextech.com
myhr.sasone.info
myhr.theax.co.in
myhrms.chhabee.in
myhrms.neotechnologysolutions.com
myhrms.shivaenterprisesvns.co.in
myhrms.theitium.com
myportal.efiltek.com
netcoredelivery.nobrokerhood.com
openings.2coms.com
orbis.sana.insure
pac.vunetsystems.com
partners.de-ha.at
partners.fyers.in
partners.zohodirectory.com
parttime.homeninjas.in
people.aciesglobal.com
people.balanciadesignstudio.com
people.blitzesports.in
people.brevitaz.com
people.caprics.in
people.chimpzlab.com
people.conformancex.com
people.deltatech.gg
people.digitalavenues.com
people.ebanitech.com
people.eximho.com
people.felizeek.com
people.fyers.in
people.infosecventures.com
people.l1supply.com
people.leegality.com
people.marlensoft.com
people.novo.co
people.prodeasy.com
people.spikewell.com
people.standardinsights.io
people.thryvalife.com
people.timelybills.app
people.trillionthoughts.com
people.viaconteam.com
people.vitalx.in
peoplestream.drivestream.com
pepipost-delivery.paytm.com
pg.mygunturmp.in
pharmacy-status.optum.com
placement.brainysensei.com
pms.bhojpur-consulting.com
pms.eedgetechnology.com
portal.acceloninc.com
portal.delhidigital.co
portal.iserveu.in
portal.limedigitalinstitute.org
pro.nexgencompany.ai
project.anrgi.com
project.luseessentials.com
projects.ancorlabs.org
projects.apconic.com
projects.brownarc.in
projects.cadopt.com
projects.callix.ai
projects.dxandbeyond.com
projects.elitekem.com
projects.fyers.in
projects.hdfc.com
projects.innovelenergy.com
projects.invenex.in
projects.ithink.co
projects.josh99.in
projects.kooticomb.in
projects.kovenantz.com
projects.krushikala.com
projects.lamirage.co
projects.mintagro.com
projects.mintskill.com
projects.nextazy.com
projects.nvish.com
projects.overturerede.us
projects.seccom.in
projects.simranboparai.in
projects.svmpl.in
projects.technopark.in
projects.theletstream.com
projects.thinkdigits.co.in
projects.touchstoneinfotech.com
projects.toybank.org
projects.truptman.com
projects.udvavisk.com
projects.webcafe.tech
projects.webistainfotech.com
projects.zerowatt.energy
projects.zodopt.com
projects.zustpe.com
pulse.securdi.com
qa.itdefined.org
qr-generation.aeronpay.in
raiseaquery.iifl.com
recruit.63moons.com
recruit.corpoz.com
recruit.eedgetechnology.com
recruit.fyers.in
recruit.varsasolutions.com
recruitment.theelixrlabs.com
regalrexnordindiaconnect.regalrexnord.com
register.phdcci.in
remote.buyerforesight.net
remote.moneyview.network
remote.omegahms.com
remote.phynyxind.com
remote.printwaresolutions.com
remote.thetelephony.co
remote.violintec.com
remoteassist.icertis.com
remoteassist.kunvarji.com
remotesupport.aqmtechnologies.com
remotesupport.infotroop.co.in
remotesupport.nvhtx.com
reports.mparadise.in
request.nirmalbang.com
resources.instacharter.app
rexnordindiaconnect.rexnord.com
rise.vnc.global
rumblings.continuousintelligence.ai
sales.mrosupplychain.in
school.shankh.tech
scrum.42andmore.com
service.iifl.com
services.gigahertzcomp.com
sharedservice.evoketechnologies.com
smesupport.iifl.com
social.simplable.com
spark.ippotamil.com
status.allium.so
status.apexdigital-m5.com
status.autotrader.co.uk
status.carsandbids.com
status.compsys.com
status.datameshgroup.com
status.extcare.com
status.fyers.in
status.gravityzone.bitdefender.com
status.greenflux.com
status.isoutsource.com
status.luhft.aro.systems
status.name-coach.com
status.nordics.worldline-solutions.com
status.out-smart.com
status.poweredbyspry.com
status.propelsoftware.com
status.retention.com
status.semita.global
status.services.mozilla.com
status.storable.com
status.stuurlui.nl
status.tribe.so
status.vcheckglobal.com
status.verso.de
status.wallester.com
students.spacetopia.in
support-desk.cloudfirst.in
support.2coms.com
support.5paisa.co.in
support.acecloudhosting.com
support.agtpl.in
support.aksharteam.com
support.ascentbusiness.com
support.asrsbot.com
support.automationedge.com
support.avarchar.com
support.babaquartz.com
support.benchmarkbroadcast.com
support.bizprospex.com
support.bornsec.com
support.bpktech.com
support.casefox.com
support.certinext.io
support.chit.biz
support.cloudiops.com
support.codemithra.com
support.craftsilicon.in
support.cymmetri.com
support.doyen.co.in
support.ecms.live
support.edhanvantari.com
support.ethnus.com
support.evokesharedservices.com
support.fleksa.com
support.futuready.solutions
support.fyers.in
support.gadgetshieldz.com
support.genailia.com
support.go4whatsup.com
support.goetc.in
support.gppae.com
support.i3web.in
support.icagroup.in
support.instavc.com
support.irmcloud.app
support.irmcloud.info
support.janatha.net
support.jashds.com
support.jaysoftshine.com
support.kgkdis.com
support.kinaracapital.com
support.kindswap.world
support.lazyadmin.in
support.madhuban.shop
support.mapusoft.com
support.marathon.in
support.marketfeed.com
support.metroride.in
support.minits.in
support.mobogage.com
support.moksatechnologies.com
support.optilinknetwork.com
support.optystack.ai
support.pathquest.com
support.paygro.in
support.pocketzines.com
support.prodeasy.com
support.qltech.com.au
support.rapidcare.ai
support.reademption.com
support.resoluteitsolutions.com
support.rmhsrp.com
support.saaral.tech
support.safeprofire.com
support.shift.in
support.shreecement.com
support.signalshq.io
support.smartaccountants.in
support.subcom.tech
support.suhsrp.com
support.synchroserve.net
support.taffinc.com
support.teclever.com
support.tisteps.co
support.transconsolution.net
support.trilegal.com
support.tv2z.com
support.xitadel.com
support.xlongevity.com
support.xoog.ai
support.yeshaenterprise.co.in
support.zenz.tech
support.zylkerinc.com
support.zypmer.com
supportdesk.fyers.in
surviellance.juzzflip.com
systemstatus.usf.edu
talent.skillscapital.io
talk.4gw.co
team.aqugen.com
team.jetquin.sbs
team.oceansoftwares.in
team.v-3.in
teams.ramanasoft.com
training.ascentsolutions.in
training.edublitz.io
trigger.bpi-philam.com
trigger.philamlife.com
unitechsupport.taffinc.com
university.smartaccountants.in
updates.aia.com.ph
usermanual.finsights.biz
velai.kaigal.com
we.lengliim.com
webinar.firstclimate.co.in
whatsnew.iifl.com
wiki.chetasee.com
workdrive.fyers.in
workflows.rmbassociates.in
workshop.pondlogs.com
workshop.yogpathwellness.com
www.alicon.ethicshelpline.in
www.arvind.ethicshelpline.in
www.cubehighways.ethicshelpline.in
www.demo1.ethicshelpline.in
www.dmart.ethicshelpline.in
www.greengas.ethicshelpline.in
www.huft.tip-offs.in
www.innow8.ai
www.instantpayout.fyers.in
www.iprg.site
www.kyberiostatus.com
www.learn.crystallotus.co
www.lisl.ethicshelpline.in
www.peakinfra.ethicshelpline.in
www.psrlawoffices.com
www.tatacoffee.ethicshelpline.in
www.tataconsumer.ethicshelpline.in
you-up.fluz.app
zoho.dev.hotstar-labs.com
zohodesk.bajajcapital.com
zohomeets.codeedoc.com
zohosupport.alfalaval.com
zyla.sana.insure
TOTAL 876
[0m
[91m[1mError: [0m"Streaming response failed: [503] The request queue is full."

===== RUN 2026-08-04 22:34:10 UTC =====
Model: deepseek | Phase: RECON | Target: fyers.in

[0m
> build · deepseek-v4-flash-free
[0m
[0m$ [0mcurl -s -m 25 -D - "https://api-docs.fyers.in/" -o /tmp/opencode/apidocs.html 2>&1 | head -40; echo "--BODY SIZE--"; wc -c /tmp/opencode/apidocs.html
HTTP/2 307 
date: Tue, 04 Aug 2026 22:33:07 GMT
content-type: text/html; charset=UTF-8
location: https://myapi.fyers.in/
strict-transport-security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
server: cloudflare
cf-ray: a261059dec5ff493-IAD

--BODY SIZE--
1107 /tmp/opencode/apidocs.html
[0m
I'll continue phase progression. Previous run completed rich RECON (DNS, CT logs, subdomain discovery). Now I'll advance to SURFACE analysis on the in-scope `*.fyers.in` API hosts, staying strictly read-only
[0m$ [0mfor h in api-docs.fyers.in marketdata-api-instaoptions.fyers.in api.fyers.in instantpayout.fyers.in workdrive.fyers.in; do echo "=== $h ==="; curl -s -o /dev/null -w "HTTP %{http_code} | server=%{header_server} | scheme=%{scheme}\n" -m 20 "https://$h/" 2>&1; done
=== api-docs.fyers.in ===
curl: unknown --write-out variable: 'header_server'
HTTP 307 | server= | scheme=HTTPS
=== marketdata-api-instaoptions.fyers.in ===
curl: unknown --write-out variable: 'header_server'
HTTP 404 | server= | scheme=HTTPS
=== api.fyers.in ===
curl: unknown --write-out variable: 'header_server'
HTTP 500 | server= | scheme=HTTPS
=== instantpayout.fyers.in ===
curl: unknown --write-out variable: 'header_server'
HTTP 000 | server= | scheme=
=== workdrive.fyers.in ===
curl: unknown --write-out variable: 'header_server'
HTTP 302 | server= | scheme=HTTPS
[0m
[0m$ [0mfor h in myapi.fyers.in api.fyers.in marketdata-api-instaoptions.fyers.in status.fyers.in; do echo "=== $h ROOT ==="; curl -s -m 20 -D - "https://$h/" -o /dev/null 2>&1 | grep -iE "HTTP/|server:|content-type:|location:|x-powered|x-fyers|x-frame|strict-transport"; done
=== myapi.fyers.in ROOT ===
HTTP/2 200 
content-type: text/html
server: cloudflare
strict-transport-security: max-age=31536000; includeSubDomains; preload
=== api.fyers.in ROOT ===
HTTP/2 500 
server: awselb/2.0
content-type: application/json; charset=utf-8
=== marketdata-api-instaoptions.fyers.in ROOT ===
HTTP/2 404 
content-type: application/json; charset=utf-8
x-powered-by: Express
strict-transport-security: max-age=31536000; includeSubDomains
=== status.fyers.in ROOT ===
HTTP/2 200 
content-type: text/html; charset=utf-8
strict-transport-security: max-age=259200
server: AtlassianEdge
[0m
[0m$ [0mecho "=== api-docs content ==="; sed 's/></>\n</g' /tmp/opencode/apidocs.html | grep -iE "href|http|location" | head; echo "=== api.fyers.in common API probe (read-only, unauthenticated) ==="; for p in v3 auth oauth data symbols login; do curl -s -o /dev/null -w "/$p -> %{http_code}\n" -m 15 "https://api.fyers.in/$p" 2>&1; done
=== api-docs content ===
=== api.fyers.in common API probe (read-only, unauthenticated) ===
/v3 -> 500
/auth -> 500
/oauth -> 500
/data -> 500
/symbols -> 500
/login -> 500
[0m
[0m$ [0mfor h in api-t1.fyers.in api-t2.fyers.in auth.fyers.in accounts.fyers.in; do echo "=== $h ==="; dig +short $h A | head -3; curl -s -o /dev/null -w "  / -> %{http_code}\n" -m 15 "https://$h/" 2>&1; done
=== api-t1.fyers.in ===
104.18.4.135
104.18.5.135
  / -> 404
=== api-t2.fyers.in ===
104.18.4.135
104.18.5.135
  / -> 503
=== auth.fyers.in ===
  / -> 000
=== accounts.fyers.in ===
  / -> 000
[0m
[0m$ [0mecho "=== api.fyers.in / v3 JSON body ==="; curl -s -m 20 "https://api.fyers.in/" 2>&1 | head -c 400; echo; echo "=== probe versioned paths ==="; for p in api/v2/validate-refresh-token api/v3/quote api/v3/history api/v3/orders api/v3/order api/v3/positions api/v3/funds api/v3/trades api/v3/gattrade api/v3/rms; do curl -s -o /dev/null -w "/$p -> %{http_code}\n" -m 15 -X GET "https://api.fyers.in/$p" 2>&1; done
=== api.fyers.in / v3 JSON body ===
{"s": "error", "code": 500, "message": "Invalid Request, please provide valid method"}
=== probe versioned paths ===
/api/v2/validate-refresh-token -> 500
/api/v3/quote -> 500
/api/v3/history -> 500
/api/v3/orders -> 500
/api/v3/order -> 500
/api/v3/positions -> 500
/api/v3/funds -> 500
/api/v3/trades -> 500
/api/v3/gattrade -> 500
/api/v3/rms -> 500
[0m
api.fyers.in (AWS ALB origin) is exposed directly without Cloudflare, while OAuth endpoints sit behind Cloudflare. The API validates HTTP method. Let me confirm method-based routing and map the OAuth surface (api-t1/t2) read-only.
[0m$ [0mecho "=== api-t1.fyers.in validate-authcode (no creds -> reveals API shape) ==="; curl -s -m 20 -D - "https://api-t1.fyers.in/api/v3/validate-authcode" -o /dev/null 2>&1 | grep -iE "HTTP/|server|content-type|allow"; echo "=== body ==="; curl -s -m 20 "https://api-t1.fyers.in/api/v3/validate-authcode" 2>&1 | head -c 300; echo; echo "=== api-t2 ==="; curl -s -m 20 -D - "https://api-t2.fyers.in/api/v3/validate-authcode" -o /dev/null 2>&1 | grep -iE "HTTP/|server"
=== api-t1.fyers.in validate-authcode (no creds -> reveals API shape) ===
HTTP/2 403 
content-type: text/html; charset=UTF-8
x-content-type-options: nosniff
server: cloudflare
=== body ===
<!DOCTYPE html>
<!--[if lt IE 7]> <html class="no-js ie6 oldie" lang="en-US"> <![endif]-->
<!--[if IE 7]>    <html class="no-js ie7 oldie" lang="en-US"> <![endif]-->
<!--[if IE 8]>    <html class="no-js ie8 oldie" lang="en-US"> <![endif]-->
<!--[if gt IE 8]><!--> <html class="no-js" lang="en-US"> <!
=== api-t2 ===
HTTP/2 403 
server: cloudflare
[0m
[0m$ [0mecho "=== method differential on api.fyers.in ==="; for m in GET POST PUT DELETE OPTIONS; do printf "%-8s " "$m"; curl -s -m 15 -X $m -o /dev/null -w "%{http_code} " "https://api.fyers.in/api/v3/order" 2>&1; curl -s -m 15 -X $m -o /dev/null -w "%{http_code}\n" -H "Content-Type: application/json" -d '{}' "https://api.fyers.in/api/v3/order" 2>&1; done
=== method differential on api.fyers.in ===
GET      500 500
POST     500 500
PUT      500 500
DELETE   500 500
OPTIONS  200 200
[0m
[0m$ [0mecho "=== OPTIONS Allow headers (method map, read-only) ==="; for p in order orders funds positions history quote trades holdings; do printf "%-10s " "$p"; curl -s -m 15 -X OPTIONS -D - -o /dev/null "https://api.fyers.in/api/v3/$p" 2>&1 | grep -i "^allow:" | tr -d '\r'; done
=== OPTIONS Allow headers (method map, read-only) ===
order      orders     funds      positions  history    quote      trades     holdings
[0m
[0m$ [0mecho "=== OPTIONS api/v3/order full body ==="; curl -s -m 15 -X OPTIONS "https://api.fyers.in/api/v3/order" 2>&1 | head -c 300; echo; echo "=== POST with bad auth to v3/order (shows error shape) ==="; curl -s -m 15 -X POST -H "Content-Type: application/json" -H "Authorization: Bearer badauth_test" -d '{}' "https://api.fyers.in/api/v3/order" 2>&1 | head -c 300; echo; echo "=== GET /api/v2/validate-refresh-token POST ==="; curl -s -m 15 -X POST -H "Content-Type: application/json" -d '{}' "https://api.fyers.in/api/v2/validate-refresh-token" 2>&1 | head -c 300
=== OPTIONS api/v3/order full body ===
OK
=== POST with bad auth to v3/order (shows error shape) ===
{"s": "error", "code": 500, "message": "Invalid Request, please provide valid method"}
=== GET /api/v2/validate-refresh-token POST ===
{"s": "error", "code": -442, "message": "Please provide valid grant_type"}
[0m
[91m[1mError: [0m"Streaming response failed: [503] The request queue is full."

===== RUN 2026-08-04 23:46:38 UTC =====
Model: deepseek | Phase: RECON | Target: fyers.in

[0m
> build · deepseek-v4-flash-free
[0m
[0m$ [0mcurl -s -m 25 "https://myapi.fyers.in/" -o /tmp/opencode/myapi.html 2>&1; wc -c /tmp/opencode/myapi.html; echo "---TITLE/LINKS---"; grep -oiE "title>[^<]+" /tmp/opencode/myapi.html | head -3; grep -oiE 'href="[^"]+"' /tmp/opencode/myapi.html | head -30
2423 /tmp/opencode/myapi.html
---TITLE/LINKS---
title>API - FYERS
href="./favicon.ico"
href="https://assets.fyers.in/images/logo.svg"
href="https://myapi.fyers.in/"
href="https://assets.fyers.in/toasters/toaster.css"
href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700&display=swap"
href="/static/css/main.367e26f7.css"
href="../style.css"
[0m
[0m$ [0mcurl -s -m 30 "https://crt.sh/?q=%25.fyers.in&output=json" -o /tmp/opencode/crt_fyers.json 2>&1; wc -c /tmp/opencode/crt_fyers.json
wc: /tmp/opencode/crt_fyers.json: No such file or directory
[0m
[0m$ [0mcurl -s -m 25 "https://myapi.fyers.in/static/js/main.367e26f7.js" -o /tmp/opencode/myapi_main.js 2>&1; wc -c /tmp/opencode/myapi_main.js; echo "---endpoints found---"; grep -oiE '"/[a-z0-9/_.-]{2,60}"' /tmp/opencode/myapi_main.js | sort -u | head -50
1485 /tmp/opencode/myapi_main.js
---endpoints found---
"/static/css/main.367e26f7.css"
"/static/js/main.2ed4e7c1.js"
[0m
[0m$ [0mfor h in api.fyers.in myapi.fyers.in api-t1.fyers.in marketdata-api-instaoptions.fyers.in status.fyers.in; do echo "=== $h headers ==="; curl -s -m 15 -D - -o /dev/null "https://$h/" 2>&1 | grep -iE "^(HTTP|server|via|x-powered|cf-ray|set-cookie|x-frame|content-security|referrer|strict|permissions|report-to|nel:|content-type)" | head -12; done
=== api.fyers.in headers ===
HTTP/2 500 
server: awselb/2.0
content-type: application/json; charset=utf-8
=== myapi.fyers.in headers ===
HTTP/2 200 
content-type: text/html
server: cloudflare
strict-transport-security: max-age=31536000; includeSubDomains; preload
set-cookie: __cf_bm=wyiyJmyCIvoxg8DRzCKUfbeMnFWCs45scLTJxSM.a.w-1785886886.7341106-1.0.1.1-6zNXram9fO4MHgACS58sLu0RkkpJMeSKpLRnfu4nStrX3a93VGdadHBcYCDvarBMB0MlXDZJALoe2HMbSk2gBy1Kwn4w8HhPgpQEChmMdcTERw8Qldwp0mDtzKEraFTd; HttpOnly; SameSite=None; Secure; Path=/; Domain=fyers.in; Expires=Wed, 05 Aug 2026 00:11:27 GMT
set-cookie: _cfuvid=ReF.FOj4DRQK1XMyXLif6b8WP8UAR8Q9m7eyAwjND2k-1785886886.7341106-1.0.1.1-ROO2VsaJU_dtRcKx24yVvgnlFRAk9l160do_fXkZECE; HttpOnly; SameSite=None; Secure; Path=/; Domain=fyers.in
cf-ray: a26169b21ef9e66c-LAX
=== api-t1.fyers.in headers ===
HTTP/2 404 
content-type: text/plain; charset=utf-8
server: cloudflare
set-cookie: __cf_bm=pGYxneoR4yW0v7Qcv.ncVndIu3tP7xwqWUkaY9heSyQ-1785886887.1202009-1.0.1.1-nmSocXGbN8K1QkahoPT71XJ3rEc3WH1yHZkgYQpllzZJsXe7Uix94pVmSHVcdRpi1LUocOJfl8e.KHJoaNsvYQ.FxA0wsDlUHtmLtSHb0jTytgjH8Wo5nhy6bL1Zkcv_; HttpOnly; SameSite=None; Secure; Path=/; Domain=fyers.in; Expires=Wed, 05 Aug 2026 00:11:27 GMT
set-cookie: _cfuvid=BmAxF.CuPM87vd..QZajm0R5.a1yiJ7iHawyQtVylSU-1785886887.1202009-1.0.1.1-EL5zyn4pJVX2dA7a_2UCnxmH1K5FR1BfN8HkIdLklfM; HttpOnly; SameSite=None; Secure; Path=/; Domain=fyers.in
strict-transport-security: max-age=31536000; includeSubDomains; preload
cf-ray: a26169b4780db7fd-LAX
=== marketdata-api-instaoptions.fyers.in headers ===
HTTP/2 404 
content-type: application/json; charset=utf-8
x-powered-by: Express
strict-transport-security: max-age=31536000; includeSubDomains
=== status.fyers.in headers ===
HTTP/2 200 
content-type: text/html; charset=utf-8
referrer-policy: strict-origin-when-cross-origin
strict-transport-security: max-age=259200
server: AtlassianEdge
report-to: {"endpoints": [{"url": "https://dz8aopenkvv6s.cloudfront.net"}], "group": "endpoint-1", "include_subdomains": true, "max_age": 600}
nel: {"failure_fraction": 0.01, "include_subdomains": true, "max_age": 600, "report_to": "endpoint-1"}
server-timing: atl-edge;dur=63,atl-edge-internal;dur=2,atl-edge-upstream;dur=62,atl-edge-pop;desc="aws-us-east-1"
via: 1.1 d9fec1199130a00161e2120cb7f67a8e.cloudfront.net (CloudFront)
[0m
[0m$ [0mcurl -s -m 45 "https://crt.sh/?q=%25.fyers.in&output=json" -o /tmp/opencode/crt_fyers.json 2>&1; wc -c /tmp/opencode/crt_fyers.json 2>/dev/null || echo "FAILED"
FAILED
[0m
[0m$ [0mcurl -s -m 30 "https://myapi.fyers.in/static/js/main.2ed4e7c1.js" -o /tmp/opencode/myapi_main2.js 2>&1; wc -c /tmp/opencode/myapi_main2.js; echo "---strings---"; grep -oiE '"[a-z0-9/_.-]{3,40}"' /tmp/opencode/myapi_main2.js | sort -u | head -60
1566261 /tmp/opencode/myapi_main2.js
---strings---
"---"
"--antd-arrow-background-color"
"--disabled"
"--selected"
"-.INF"
"-.Inf"
"-.inf"
"-0.0"
"-0.25em"
"-0.5em"
"-0b"
"-0o"
"-0x"
"-180deg"
"-1px"
"-25px"
"-2px"
"-90deg"
"-active"
"-and-"
"-ant-"
"-arrow"
"-arrow-content"
"-background-ghost"
"-block"
"-body"
"-body-wrapper"
"-btn"
"-btns"
"-centered"
"-click-animating"
"-click-animating-node"
"-click-animating-without-extra-node"
"-closable"
"-close"
"-close-icon"
"-close-x"
"-color"
"-color-active"
"-color-deprecated-bg"
"-color-deprecated-border"
"-color-disabled"
"-color-hover"
"-color-outline"
"-confirm"
"-content"
"-custom-content"
"-dangerous"
"-depth"
"-description"
"-disabled"
"-disabled-compatible-wrapper"
"-dynamic-theme"
"-footer"
"-grow"
"-header"
"-hidden"
"-hook-holder"
"-icon"
"-icon-"
[0m
[0m$ [0mcurl -s -m 30 "https://fyers.in/robots.txt" 2>&1 | head -20; echo "---sitemap?---"; curl -s -o /dev/null -w "robots %{http_code}\n" -m 15 "https://fyers.in/robots.txt"
User-agent: *

Allow: /*

Sitemap: https://fyers.in/sitemap.xml

Disallow: /community/search?
Disallow: /community/rss/
Disallow: /community/member/
Disallow: /community/product-feedbacks/
Disallow: /community/fyers-one-4mrwepl4/
Disallow: /community/fyers-direct-7q3z9sap/
Disallow: /community/technical-analysis-xzebqjvq/
Disallow: /community/fyers-app-oif71184/
Disallow: /community/verified-pnl-3tinex2d/
Disallow: /community/f-o-hv6kiev0/
Disallow: /community/tradingview-qnlz2et2/
Disallow: /community/partners-platforms/
Disallow: /community/pledge-kim93hax/
Disallow: /community/demat-account-tv337cne/
---sitemap?---
robots 200
[0m
[0m$ [0msleep 5; curl -s -m 60 "https://crt.sh/?q=%25.fyers.in&output=json" -o /tmp/opencode/crt_fyers.json 2>&1; wc -c /tmp/opencode/crt_fyers.json 2>/dev/null || echo FAILED
150 /tmp/opencode/crt_fyers.json
[0m
[0m$ [0mcat /tmp/opencode/crt_fyers.json; echo; echo "---alt CT source---"; curl -s -m 45 "https://crt.sh/?q=*.fyers.in&output=json" -o /tmp/opencode/crt_fyers2.json 2>&1; wc -c /tmp/opencode/crt_fyers2.json
<html>
<head><title>502 Bad Gateway</title></head>
<body>
<center><h1>502 Bad Gateway</h1></center>
<hr><center>nginx</center>
</body>
</html>

---alt CT source---
150 /tmp/opencode/crt_fyers2.json
[0m
[0m$ [0mgrep -oiE "(https?:)?//[a-z0-9.-]*fyers\.in[a-z0-9/_.-]*" /tmp/opencode/myapi_main2.js | sort -u | head -40; echo "---api path literals---"; grep -oiE "/api/[a-z0-9/_-]{2,40}" /tmp/opencode/myapi_main2.js | sort -u | head -40
https://alerts.fyers.in
https://api-connect-docs.fyers.in/
https://api-t1.fyers.in/api/v2/user
https://api-t1.fyers.in/gk/verify_token
https://assets.fyers.in
https://assets.fyers.in/Marcom_Avatar/Images/fyers-api-c.svg
https://assets.fyers.in/Marcom_Avatar/Images/fyers-api-go.svg
https://assets.fyers.in/images/Fyers-Myapi/api-connect.svg
https://assets.fyers.in/images/Fyers-Myapi/icon-algorthmic-trading.svg
https://assets.fyers.in/images/Fyers-Myapi/icon-free.svg
https://assets.fyers.in/images/Fyers-Myapi/icon-platforms.svg
https://assets.fyers.in/images/Fyers-Myapi/icon-robust.svg
https://assets.fyers.in/images/Fyers-Myapi/icon-simple.svg
https://assets.fyers.in/images/Fyers-Myapi/icon-startups.svg
https://assets.fyers.in/images/Fyers-Myapi/illustration-corporate.svg
https://assets.fyers.in/images/Fyers-Myapi/node-js.svg
https://assets.fyers.in/images/Fyers-Myapi/python.svg
https://assets.fyers.in/images/Fyers-Myapi/rest.svg
https://assets.fyers.in/images/global-header/fyers-pledge-icon.svg
https://assets.fyers.in/images/pledge/logo-holiday-calender.svg
https://assets.fyers.in/images/pledge/logo-insight.svg
https://assets.fyers.in/images/pledge/logo-noticeboard.svg
https://assets.fyers.in/images/pledge/logo-sos.svg
https://assets.fyers.in/images/website/Fyers-sgb-icon.svg
https://assets.fyers.in/images/website/api/integration-methods/c-sharp.svg
https://assets.fyers.in/images/website/api/integration-methods/java.svg
https://assets.fyers.in/images/website/api/integration-methods/js.svg
https://assets.fyers.in/images/website/icon-brokerage-calculator.svg
https://assets.fyers.in/images/website/icon-fyers-api.svg
https://assets.fyers.in/images/website/icon-fyers-app.svg
https://assets.fyers.in/images/website/icon-fyers-direct.svg
https://assets.fyers.in/images/website/icon-fyers-one.svg
https://assets.fyers.in/images/website/icon-fyers-web.svg
https://assets.fyers.in/images/website/icon-ipo.svg
https://assets.fyers.in/images/website/icon-link-next.svg
https://assets.fyers.in/images/website/icon-margin-calculator.svg
https://assets.fyers.in/images/website/icon-ofs.svg
https://assets.fyers.in/images/website/icon-price-alerts.svg
https://community.fyers.in/fyers-api-rha0riqv
https://direct.fyers.in
---api path literals---
/api/integration-methods/c-sharp
/api/integration-methods/java
/api/integration-methods/js
/api/v2/user
[0m
[0m$ [0mecho "=== security.txt ==="; curl -s -m 15 "https://fyers.in/.well-known/security.txt" | head -30; echo "=== security page ==="; for p in security disclosure responsible-disclosure bug-bounty; do curl -s -o /dev/null -w "/$p -> %{http_code}\n" -m 15 "https://fyers.in/$p"; done
=== security.txt ===
<!DOCTYPE html><!--q4ywsrT7XC5dYY456F9Ym--><html lang="en" class="__variable_6b03d3 __variable_96b523 __variable_a3085e __variable_48b81b"><head><meta charSet="utf-8"/><meta name="viewport" content="width=device-width, initial-scale=1"/><link rel="preload" href="/_next/static/media/4c9affa5bc8f420e-s.p.woff2" as="font" crossorigin="" type="font/woff2"/><link rel="preload" href="/_next/static/media/636a5ac981f94f8b-s.p.woff2" as="font" crossorigin="" type="font/woff2"/><link rel="preload" href="/_next/static/media/9f00717ceecbaf1c-s.p.woff2" as="font" crossorigin="" type="font/woff2"/><link rel="preload" href="/_next/static/media/c49dcee81b580683-s.p.woff2" as="font" crossorigin="" type="font/woff2"/><link rel="preload" href="/_next/static/media/e4af272ccee01ff0-s.p.woff2" as="font" crossorigin="" type="font/woff2"/><link rel="stylesheet" href="/_next/static/css/ce4586793c162069.css" data-precedence="next"/><link rel="stylesheet" href="/_next/static/css/e2edf395d8a7c67d.css" data-precedence="next"/><link rel="preload" as="script" fetchPriority="low" href="/_next/static/chunks/webpack-6c822a2274189e88.js"/><script src="/_next/static/chunks/4bd1b696-100b9d70ed4e49c1.js" async=""></script><script src="/_next/static/chunks/1255-8befde0980f5cba9.js" async=""></script><script src="/_next/static/chunks/main-app-74bcf3f11e0e13d8.js" async=""></script><script src="/_next/static/chunks/app/layout-0e213fa8ced99c57.js" async=""></script><meta name="robots" content="noindex"/><meta name="next-size-adjust" content=""/><link rel="dns-prefetch" href="https://assets.fyers.in"/><title>404: This page could not be found.</title><title>Online Stock Trading &amp; Investment Platform in India | FYERS</title><meta name="description" content="Start trading in equities, f&amp;o, etc. Access advanced charting, fast order execution, and APIs for smart trading by opening your free demat account with FYERS."/><meta name="keywords" content="stock trading,investment,fyers,demat account"/><meta name="mobile-web-app-capable" content="yes"/><meta name="apple-mobile-web-app-title" content="FYERS"/><meta name="apple-mobile-web-app-status-bar-style" content="black"/><meta property="og:title" content="Online Stock Trading &amp; Investment Platform in India | FYERS"/><meta property="og:description" content="Start trading in equities, f&amp;o, etc. Access advanced charting, fast order execution, and APIs for smart trading by opening your free demat account with FYERS."/><meta property="og:url" content="https://fyers.in"/><meta property="og:site_name" content="FYERS"/><meta property="og:locale" content="en_GB"/><meta property="og:image" content="https://assets.fyers.in/Marcom_Avatar/Images/meta-image/home.jpg"/><meta property="og:image:width" content="1200"/><meta property="og:image:height" content="630"/><meta property="og:image:alt" content="Fyers Trading Simplified"/><meta property="og:type" content="website"/><meta name="twitter:card" content="summary"/><meta name="twitter:site" content="@fyers1"/><meta name="twitter:creator" content="@fyers1"/><meta name="twitter:title" content="Online Stock Trading &amp; Investment Platform in India | FYERS"/><meta name="twitter:description" content="Start trading in equities, f&amp;o, etc. Access advanced charting, fast order execution, and APIs for smart trading by opening your free demat account with FYERS."/><meta name="twitter:image" content="https://assets.fyers.in/Marcom_Avatar/Images/meta-image/home.jpg"/><link rel="shortcut icon" href="/favicon.ico"/><link rel="icon" href="/favicon.ico" type="image/x-icon" sizes="32x32"/><link rel="icon" href="/favicon.ico" sizes="any"/><link rel="icon" href="/favicon-32x32.png" sizes="32x32" type="image/png"/><link rel="icon" href="/favicon-192x192.png" sizes="192x192" type="image/png"/><link rel="apple-touch-icon" href="/apple-touch-icon.png"/><script type="application/ld+json">{"@context":"https://schema.org","@type":"Organization","name":"FYERS","url":"https://fyers.in","logo":"https://assets.fyers.in/Marcom_Avatar/Images/fyers.svg","description":"Online Stock Trading & Investment Platform in India","sameAs":["https://www.facebook.com/fyers1/","https://twitter.com/fyers1","https://www.instagram.com/fyers_official","https://www.linkedin.com/company/fyers-securities-pvt-ltd"]}</script><script src="/_next/static/chunks/polyfills-42372ed130431b0a.js" noModule=""></script></head><body><div hidden=""><!--$--><!--/$--></div><noscript><iframe src="https://www.googletagmanager.com/ns.html?id=GTM-MB6PRVDG" height="0" width="0" style="display:none;visibility:hidden"></iframe></noscript><div style="font-family:system-ui,&quot;Segoe UI&quot;,Roboto,Helvetica,Arial,sans-serif,&quot;Apple Color Emoji&quot;,&quot;Segoe UI Emoji&quot;;height:100vh;text-align:center;display:flex;flex-direction:column;align-items:center;justify-content:center"><div><style>body{color:#000;background:#fff;margin:0}.next-error-h1{border-right:1px solid rgba(0,0,0,.3)}@media (prefers-color-scheme:dark){body{color:#fff;background:#000}.next-error-h1{border-right:1px solid rgba(255,255,255,.3)}}</style><h1 class="next-error-h1" style="display:inline-block;margin:0 20px 0 0;padding:0 23px 0 0;font-size:24px;font-weight:500;vertical-align:top;line-height:49px">404</h1><div style="display:inline-block"><h2 style="font-size:14px;font-weight:400;line-height:49px;margin:0">This page could not be found.</h2></div></div></div><!--$--><!--/$--><button type="button" aria-label="Scroll to top" tabindex="-1" style="bottom:calc(var(--mobile-sticky-signup-height, 0px) + 16px)" class="fixed right-6 z-50 flex cursor-pointer items-center justify-center rounded-sm bg-[#182BFF] px-3 py-3 shadow-lg transition-all duration-300 ease-out hover:opacity-95 focus:outline-none focus-visible:ring-2 focus-visible:ring-[#F0F0FA] focus-visible:ring-offset-2 focus-visible:ring-offset-[#131319] xl:size-[60px] xl:aspect-square xl:p-0 xl:!bottom-6 pointer-events-none opacity-0"><span class="flex items-center justify-center not-italic font-manrope text-[#F0F0FA]"><svg class="xl:size-6" width="16" height="16" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg"><path d="M12.6375 2.55288L19.8668 9.81265L20.502 10.4501L19.227 11.7205L18.5918 11.083L12.9 5.36772V22.088H11.1V5.36772L5.4082 11.083L4.77305 11.7205L3.49805 10.4501L4.1332 9.81265L11.3613 2.55288L11.9988 1.91187L12.6375 2.55288Z" fill="white"></path></svg></span></button><script src="/_next/static/chunks/webpack-6c822a2274189e88.js" id="_R_" async=""></script><script>(self.__next_f=self.__next_f||[]).push([0])</script><script>self.__next_f.push([1,"1:\"$Sreact.fragment\"\n2:I[71835,[\"7177\",\"static/chunks/app/layout-0e213fa8ced99c57.js\"],\"UserProvider\"]\n3:I[75545,[\"7177\",\"static/chunks/app/layout-0e213fa8ced99c57.js\"],\"default\"]\n4:I[41402,[\"7177\",\"static/chunks/app/layout-0e213fa8ced99c57.js\"],\"\"]\n5:I[37126,[\"7177\",\"static/chunks/app/layout-0e213fa8ced99c57.js\"],\"default\"]\n6:I[58226,[\"7177\",\"static/chunks/app/layout-0e213fa8ced99c57.js\"],\"default\"]\n7:I[9766,[],\"\"]\n8:I[98924,[],\"\"]\n9:I[63905,[\"7177\",\"static/chunks/app/layout-0e213fa8ced99c57.js\"],\"default\"]\na:I[24431,[],\"OutletBoundary\"]\nc:I[15278,[],\"AsyncMetadataOutlet\"]\ne:I[24431,[],\"ViewportBoundary\"]\n10:I[24431,[],\"MetadataBoundary\"]\n11:\"$Sreact.suspense\"\n13:I[57150,[],\"\"]\n:HL[\"/_next/static/media/4c9affa5bc8f420e-s.p.woff2\",\"font\",{\"crossOrigin\":\"\",\"type\":\"font/woff2\"}]\n:HL[\"/_next/static/media/636a5ac981f94f8b-s.p.woff2\",\"font\",{\"crossOrigin\":\"\",\"type\":\"font/woff2\"}]\n:HL[\"/_next/static/media/9f00717ceecbaf1c-s.p.woff2\",\"font\",{\"crossOrigin\":\"\",\"type\":\"font/woff2\"}]\n:HL[\"/_next/static/media/c49dcee81b580683-s.p.woff2\",\"font\",{\"crossOrigin\":\"\",\"type\":\"font/woff2\"}]\n:HL[\"/_next/static/media/e4af272ccee01ff0-s.p.woff2\",\"font\",{\"crossOrigin\":\"\",\"type\":\"font/woff2\"}]\n:HL[\"/_next/static/css/ce4586793c162069.css\",\"style\"]\n:HL[\"/_next/static/css/e2edf395d8a7c67d.css\",\"style\"]\n"])</script><script>self.__next_f.push([1,"0:{\"P\":null,\"b\":\"q4ywsrT7XC5dYY456F9Ym\",\"p\":\"\",\"c\":[\"\",\"_not-found\"],\"i\":false,\"f\":[[[\"\",{\"children\":[\"/_not-found\",{\"children\":[\"__PAGE__\",{}]}]},\"$undefined\",\"$undefined\",true],[\"\",[\"$\",\"$1\",\"c\",{\"children\":[[[\"$\",\"link\",\"0\",{\"rel\":\"stylesheet\",\"href\":\"/_next/static/css/ce4586793c162069.css\",\"precedence\":\"next\",\"crossOrigin\":\"$undefined\",\"nonce\":\"$undefined\"}],[\"$\",\"link\",\"1\",{\"rel\":\"stylesheet\",\"href\":\"/_next/static/css/e2edf395d8a7c67d.css\",\"precedence\":\"next\",\"crossOrigin\":\"$undefined\",\"nonce\":\"$undefined\"}]],[\"$\",\"html\",null,{\"lang\":\"en\",\"className\":\"__variable_6b03d3 __variable_96b523 __variable_a3085e __variable_48b81b\",\"children\":[[\"$\",\"head\",null,{\"children\":[[\"$\",\"script\",null,{\"type\":\"application/ld+json\",\"dangerouslySetInnerHTML\":{\"__html\":\"{\\\"@context\\\":\\\"https://schema.org\\\",\\\"@type\\\":\\\"Organization\\\",\\\"name\\\":\\\"FYERS\\\",\\\"url\\\":\\\"https://fyers.in\\\",\\\"logo\\\":\\\"https://assets.fyers.in/Marcom_Avatar/Images/fyers.svg\\\",\\\"description\\\":\\\"Online Stock Trading \u0026 Investment Platform in India\\\",\\\"sameAs\\\":[\\\"https://www.facebook.com/fyers1/\\\",\\\"https://twitter.com/fyers1\\\",\\\"https://www.instagram.com/fyers_official\\\",\\\"https://www.linkedin.com/company/fyers-securities-pvt-ltd\\\"]}\"}}],[\"$\",\"link\",null,{\"rel\":\"dns-prefetch\",\"href\":\"https://assets.fyers.in\"}]]}],[\"$\",\"body\",null,{\"children\":[\"$\",\"$L2\",null,{\"children\":[[\"$\",\"$L3\",null,{\"gtmId\":\"GTM-MB6PRVDG\"}],[\"$\",\"$L4\",null,{\"id\":\"gtag-js\",\"src\":\"https://www.googletagmanager.com/gtag/js?id=G-JXG5NQ1WQJ\",\"strategy\":\"lazyOnload\"}],[\"$\",\"$L4\",null,{\"id\":\"gtag-init\",\"strategy\":\"lazyOnload\",\"children\":\"\\n              window.dataLayer = window.dataLayer || [];\\n              function gtag(){dataLayer.push(arguments);}\\n              gtag('js', new Date());\\n              gtag('config', 'G-JXG5NQ1WQJ', { anonymize_ip: true });\\n            \"}],[\"$\",\"$L5\",null,{}],[\"$\",\"$L6\",null,{}],[\"$\",\"$L7\",null,{\"parallelRouterKey\":\"children\",\"error\":\"$undefined\",\"errorStyles\":\"$undefined\",\"errorScripts\":\"$undefined\",\"template\":[\"$\",\"$L8\",null,{}],\"templateStyles\":\"$undefined\",\"templateScripts\":\"$undefined\",\"notFound\":\"$undefined\",\"forbidden\":\"$undefined\",\"unauthorized\":\"$undefined\"}],[\"$\",\"$L9\",null,{}]]}]}]]}]]}],{\"children\":[\"/_not-found\",[\"$\",\"$1\",\"c\",{\"children\":[null,[\"$\",\"$L7\",null,{\"parallelRouterKey\":\"children\",\"error\":\"$undefined\",\"errorStyles\":\"$undefined\",\"errorScripts\":\"$undefined\",\"template\":[\"$\",\"$L8\",null,{}],\"templateStyles\":\"$undefined\",\"templateScripts\":\"$undefined\",\"notFound\":\"$undefined\",\"forbidden\":\"$undefined\",\"unauthorized\":\"$undefined\"}]]}],{\"children\":[\"__PAGE__\",[\"$\",\"$1\",\"c\",{\"children\":[[[\"$\",\"title\",null,{\"children\":\"404: This page could not be found.\"}],[\"$\",\"div\",null,{\"style\":{\"fontFamily\":\"system-ui,\\\"Segoe UI\\\",Roboto,Helvetica,Arial,sans-serif,\\\"Apple Color Emoji\\\",\\\"Segoe UI Emoji\\\"\",\"height\":\"100vh\",\"textAlign\":\"center\",\"display\":\"flex\",\"flexDirection\":\"column\",\"alignItems\":\"center\",\"justifyContent\":\"center\"},\"children\":[\"$\",\"div\",null,{\"children\":[[\"$\",\"style\",null,{\"dangerouslySetInnerHTML\":{\"__html\":\"body{color:#000;background:#fff;margin:0}.next-error-h1{border-right:1px solid rgba(0,0,0,.3)}@media (prefers-color-scheme:dark){body{color:#fff;background:#000}.next-error-h1{border-right:1px solid rgba(255,255,255,.3)}}\"}}],[\"$\",\"h1\",null,{\"className\":\"next-error-h1\",\"style\":{\"display\":\"inline-block\",\"margin\":\"0 20px 0 0\",\"padding\":\"0 23px 0 0\",\"fontSize\":24,\"fontWeight\":500,\"verticalAlign\":\"top\",\"lineHeight\":\"49px\"},\"children\":404}],[\"$\",\"div\",null,{\"style\":{\"display\":\"inline-block\"},\"children\":[\"$\",\"h2\",null,{\"style\":{\"fontSize\":14,\"fontWeight\":400,\"lineHeight\":\"49px\",\"margin\":0},\"children\":\"This page could not be found.\"}]}]]}]}]],null,[\"$\",\"$La\",null,{\"children\":[\"$Lb\",[\"$\",\"$Lc\",null,{\"promise\":\"$@d\"}]]}]]}],{},null,false]},null,false]},null,false],[\"$\",\"$1\",\"h\",{\"children\":[[\"$\",\"meta\",null,{\"name\":\"robots\",\"content\":\"noindex\"}],[[\"$\",\"$Le\",null,{\"children\":\"$Lf\"}],[\"$\",\"meta\",null,{\"name\":\"next-size-adjust\",\"content\":\"\"}]],[\"$\",\"$L10\",null,{\"children\":[\"$\",\"div\",null,{\"hidden\":true,\"children\":[\"$\",\"$11\",null,{\"fallback\":null,\"children\":\"$L12\"}]}]}]]}],false]],\"m\":\"$undefined\",\"G\":[\"$13\",[]],\"s\":false,\"S\":true}\n"])</script><script>self.__next_f.push([1,"f:[[\"$\",\"meta\",\"0\",{\"charSet\":\"utf-8\"}],[\"$\",\"meta\",\"1\",{\"name\":\"viewport\",\"content\":\"width=device-width, initial-scale=1\"}]]\nb:null\n"])</script><script>self.__next_f.push([1,"14:I[80622,[],\"IconMark\"]\n"])</script><script>self.__next_f.push([1,"d:{\"metadata\":[[\"$\",\"title\",\"0\",{\"children\":\"Online Stock Trading \u0026 Investment Platform in India | FYERS\"}],[\"$\",\"meta\",\"1\",{\"name\":\"description\",\"content\":\"Start trading in equities, f\u0026o, etc. Access advanced charting, fast order execution, and APIs for smart trading by opening your free demat account with FYERS.\"}],[\"$\",\"meta\",\"2\",{\"name\":\"keywords\",\"content\":\"stock trading,investment,fyers,demat account\"}],[\"$\",\"meta\",\"3\",{\"name\":\"mobile-web-app-capable\",\"content\":\"yes\"}],[\"$\",\"meta\",\"4\",{\"name\":\"apple-mobile-web-app-title\",\"content\":\"FYERS\"}],[\"$\",\"meta\",\"5\",{\"name\":\"apple-mobile-web-app-status-bar-style\",\"content\":\"black\"}],[\"$\",\"meta\",\"6\",{\"property\":\"og:title\",\"content\":\"Online Stock Trading \u0026 Investment Platform in India | FYERS\"}],[\"$\",\"meta\",\"7\",{\"property\":\"og:description\",\"content\":\"Start trading in equities, f\u0026o, etc. Access advanced charting, fast order execution, and APIs for smart trading by opening your free demat account with FYERS.\"}],[\"$\",\"meta\",\"8\",{\"property\":\"og:url\",\"content\":\"https://fyers.in\"}],[\"$\",\"meta\",\"9\",{\"property\":\"og:site_name\",\"content\":\"FYERS\"}],[\"$\",\"meta\",\"10\",{\"property\":\"og:locale\",\"content\":\"en_GB\"}],[\"$\",\"meta\",\"11\",{\"property\":\"og:image\",\"content\":\"https://assets.fyers.in/Marcom_Avatar/Images/meta-image/home.jpg\"}],[\"$\",\"meta\",\"12\",{\"property\":\"og:image:width\",\"content\":\"1200\"}],[\"$\",\"meta\",\"13\",{\"property\":\"og:image:height\",\"content\":\"630\"}],[\"$\",\"meta\",\"14\",{\"property\":\"og:image:alt\",\"content\":\"Fyers Trading Simplified\"}],[\"$\",\"meta\",\"15\",{\"property\":\"og:type\",\"content\":\"website\"}],[\"$\",\"meta\",\"16\",{\"name\":\"twitter:card\",\"content\":\"summary\"}],[\"$\",\"meta\",\"17\",{\"name\":\"twitter:site\",\"content\":\"@fyers1\"}],[\"$\",\"meta\",\"18\",{\"name\":\"twitter:creator\",\"content\":\"@fyers1\"}],[\"$\",\"meta\",\"19\",{\"name\":\"twitter:title\",\"content\":\"Online Stock Trading \u0026 Investment Platform in India | FYERS\"}],[\"$\",\"meta\",\"20\",{\"name\":\"twitter:description\",\"content\":\"Start trading in equities, f\u0026o, etc. Access advanced charting, fast order execution, and APIs for smart trading by opening your free demat account with FYERS.\"}],[\"$\",\"meta\",\"21\",{\"name\":\"twitter:image\",\"content\":\"https://assets.fyers.in/Marcom_Avatar/Images/meta-image/home.jpg\"}],[\"$\",\"link\",\"22\",{\"rel\":\"shortcut icon\",\"href\":\"/favicon.ico\"}],[\"$\",\"link\",\"23\",{\"rel\":\"icon\",\"href\":\"/favicon.ico\",\"type\":\"image/x-icon\",\"sizes\":\"32x32\"}],[\"$\",\"link\",\"24\",{\"rel\":\"icon\",\"href\":\"/favicon.ico\",\"sizes\":\"any\"}],[\"$\",\"link\",\"25\",{\"rel\":\"icon\",\"href\":\"/favicon-32x32.png\",\"sizes\":\"32x32\",\"type\":\"image/png\"}],[\"$\",\"link\",\"26\",{\"rel\":\"icon\",\"href\":\"/favicon-192x192.png\",\"sizes\":\"192x192\",\"type\":\"image/png\"}],[\"$\",\"link\",\"27\",{\"rel\":\"apple-touch-icon\",\"href\":\"/apple-touch-icon.png\"}],[\"$\",\"$L14\",\"28\",{}]],\"error\":null,\"digest\":\"$undefined\"}\n"])</script><script>self.__next_f.push([1,"12:\"$d:metadata\"\n"])</script></body></html>=== security page ===
/security -> 404
/disclosure -> 200
/responsible-disclosure -> 404
/bug-bounty -> 404
[0m
[0m$ [0mcurl -s -m 20 "https://fyers.in/disclosure" -o /tmp/opencode/disclosure.html 2>&1; echo "--- policy text ---"; python3 -c "
import re,html
d=open('/tmp/opencode/disclosure.html').read()
d=re.sub(r'<script.*?</script>','',d,flags=re.S)
d=re.sub(r'<style.*?</style>','',d,flags=re.S)
t=re.sub(r'<[^>]+>',' ',d)
t=html.unescape(t)
t=re.sub(r'\s+',' ',t)
print(t[:3500])
"
--- policy text ---
 Online Stock Trading & Investment Platform in India | FYERS Products Offerings Pricing Learn About More Login Sign Up Disclosure The website is owned, operated and maintained by FYERS Securities Private Limited (hereinafter referred to as “FYERS” or "fyers.in"), a company incorporated under the Companies Act, 2013 having CIN: U65990KA2018PTC118169, and having its registered office at 901 and 902, 9th Floor, A Wing, Brigade Magnum, Amruthahalli Kodigehalli Gate, Hebbal, Bangalore, Karnataka, India, 560092.This site, including all content or information contained within it or any site linked to the site, or any product or service licensed or purchased through the site, is provided on an "as is" basis and without warranties of any kind, either express or implied.including, but not limited to warranties of title or non-infringement or warranties of merchantability and fitness for a particular purpose,other than those warranties which are implied by and incapable of exclusion, restriction or modification under the laws applicable to this disclaimer. Access to and use of this site and the information provided herein is at your own risk and fyers.in does not undertake any accountability for any irregularities, viruses or damage to any computer that results from accessing, availing or downloading of any information from this site. Fyers.in does not guarantee that your access to the site and/or related services will be uninterrupted or error-free, that defects will be corrected, or that this site or the server provided herein is free of viruses or other harmful components.FYERS does not warrant or make any representations regarding the use of or any consequences resulting from the use of any product and/or service purchased in terms of its compatibility, correctness, accuracy, and reliability or otherwise.You assume total responsibility and risk for your use of this site and all site-related services. Unless expressly provided, FYERS and its directors, officers, employees, agents, or other representatives ('service providers') shall not be responsible or liable for any direct, indirect, incidental, consequential, special, exemplary, punitive or any other damages (including without limitation loss of profits, loss or corruption of data, loss of goodwill, work stoppage, computer failure or malfunction, or interruption of business) under any contract, relationship, negligence, strict liability or any other theory arising out of or relating in any way with the use of the site or in reliance of the information available on the site, site-related services, or any products or services offered or sold or displayed on the fyers.in.If the foregoing limitation is held to be unenforceable, the maximum liability of FYERS to you shall not exceed the amount of fees paid by you for the products or services that you have ordered through the site. There is a possibility that this site could include inaccuracies or errors. Additionally, the possibility exists that unauthorized additions, deletions or alterations could be made by third parties to the site. Although FYERS attempts to ensure the highest level of security, integrity, correctness and authenticity of the site, it makes no guarantees whatsoever as to its completeness, correctness or accuracy.In the event that any inaccuracy arises, please inform [email protected] so that it can be corrected. Please note that past performance is not necessarily an indicator of future performance. The services referred to
[0m
[0m$ [0mfor h in alerts.fyers.in api-connect-docs.fyers.in community.fyers.in direct.fyers.in; do echo "=== $h ==="; curl -s -o /dev/null -w "HTTP %{http_code} type=%{content_type} " -m 15 "https://$h/" 2>&1; curl -s -m 15 -D - -o /dev/null "https://$h/" 2>&1 | grep -iE "^(server|location|x-powered|via):" | head -3; done
=== alerts.fyers.in ===
HTTP 301 type=text/html; charset=UTF-8 location: https://fyers.in/web/orders/stocks_and_fno/alerts
server: cloudflare
=== api-connect-docs.fyers.in ===
HTTP 200 type=text/html server: cloudflare
=== community.fyers.in ===
HTTP 301 type=text/html; charset=UTF-8 location: https://fyers.in/community/
server: cloudflare
=== direct.fyers.in ===
HTTP 301 type=text/html; charset=UTF-8 location: https://fyers.in/web/mutual-funds/home
server: cloudflare
[0m
[0m$ [0mecho "=== fyers.in/web ==="; curl -s -m 20 -D - -o /tmp/opencode/web.html "https://fyers.in/web/orders/stocks_and_fno/positions" 2>&1 | grep -iE "^(HTTP|server|location|x-powered|content-type)"; echo "=== web.html head ==="; grep -oiE '(<title>[^<]+|src="[^"]+\.js[^"]*")' /tmp/opencode/web.html 2>/dev/null | head -10
=== fyers.in/web ===
HTTP/2 200 
content-type: text/html
server: cloudflare
=== web.html head ===
<title>FYERS - Web
src="/web/assets/packages/flutter_inappwebview_web/assets/web/web_support.js"
src="js_dart_service.js"
src="./assets/packages/clevertap_plugin/assets/clevertap.js"
[0m
[0m$ [0mgrep -oE "api-t[12]\.fyers\.in/api/v[23]/[a-zA-Z0-9/_-]+" /tmp/opencode/myapi_main2.js | sort -u | head -50
api-t1.fyers.in/api/v2/user
[0m
[0m$ [0mecho "=== api-connect-docs ==="; curl -s -m 20 "https://api-connect-docs.fyers.in/" -o /tmp/opencode/apidocs2.html 2>&1; wc -c /tmp/opencode/apidocs2.html; grep -oiE 'href="[^"]+"' /tmp/opencode/apidocs2.html | head -30
=== api-connect-docs ===
10904 /tmp/opencode/apidocs2.html
href="/img/fyers-favicon.png"
href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.12.0/styles/rainbow.min.css"
href="/css/code-block-buttons.css"
href="https://fyers.in/wp-content/plugins/tlp-team/assets/vendor/font-awesome/css/font-awesome.min.css?ver=2.5.2"
href="https://fyers.in/wp-content/themes/salient/css/font-awesome.min.css?ver=4.6.3"
href="/css/main.css"
href="/"
href="https://myapi.fyers.in"
href="/demo.html"
href="/demo.html"
href="/docs/order"
[0m
[0m$ [0mcurl -s -m 30 "https://api.certspotter.com/v1/issuances?domain=fyers.in&include_subdomains=true&expand=dns_names" -o /tmp/opencode/certspotter.json 2>&1; wc -c /tmp/opencode/certspotter.json; python3 -c "
import json
try:
    d=json.load(open('/tmp/opencode/certspotter.json'))
    names=set()
    for e in d:
        for n in e.get('dns_names',[]):
            if n.endswith('.fyers.in') or n=='fyers.in': names.add(n.lower())
    for n in sorted(names): print(n)
    print('TOTAL', len(names))
except Exception as ex:
    print('ERR', ex)
" 2>&1 | head -80
185176 /tmp/opencode/certspotter.json
*.fyers.in
assist.fyers.in
cliq.fyers.in
connect.fyers.in
delivery.fyers.in
forms.fyers.in
fyers.in
instantpayout.fyers.in
learn.fyers.in
marketdata-api-instaoptions.fyers.in
meeting.fyers.in
partners.fyers.in
people.fyers.in
projects.fyers.in
recruit.fyers.in
status.fyers.in
support.fyers.in
supportdesk.fyers.in
workdrive.fyers.in
www.instantpayout.fyers.in
TOTAL 20
[0m
[0m$ [0mfor p in docs docs/order docs/authentication docs/trade docs/data docs/history docs/quote demo.html; do curl -s -o /dev/null -w "/$p -> %{http_code}\n" -m 15 "https://api-connect-docs.fyers.in/$p"; done; echo "---docs nav---"; curl -s -m 15 "https://api-connect-docs.fyers.in/" | grep -oE 'href="/[^"]*"' | sort -u | head -40
/docs -> 404
/docs/order -> 302
/docs/authentication -> 404
/docs/trade -> 404
/docs/data -> 404
/docs/history -> 404
/docs/quote -> 404
/demo.html -> 200
---docs nav---
href="/"
href="//cdnjs.cloudflare.com/ajax/libs/highlight.js/9.12.0/styles/rainbow.min.css"
href="/css/code-block-buttons.css"
href="/css/main.css"
href="/demo.html"
href="/docs/order"
href="/img/fyers-favicon.png"
[0m
[0m$ [0mcurl -s -m 15 -D - -o /tmp/opencode/order_doc.html "https://api-connect-docs.fyers.in/docs/order" 2>&1 | grep -iE "^(HTTP|location)"; echo "---body title---"; grep -oiE "title>[^<]+" /tmp/opencode/order_doc.html | head -2; grep -oE 'href="/[^"]*"' /tmp/opencode/order_doc.html | sort -u | head -50
HTTP/2 302 
location: /docs/order/
---body title---
title>302 Moved Temporarily
[0m
[0m$ [0mcurl -s -m 30 "https://fyers.in/sitemap.xml" -o /tmp/opencode/sitemap.xml 2>&1; grep -oE "<loc>[^<]+" /tmp/opencode/sitemap.xml | sed 's/<loc>//' | head -60
https://fyers.in/products-sitemap.xml
https://fyers.in/calculator-sitemap.xml
https://fyers.in/support-sitemap.xml
https://fyers.in/community-sitemap.xml
https://fyers.in/noticeboard-sitemap.xml
https://fyers.in/blog-sitemap.xml
https://fyers.in/others-sitemap.xml
https://fyers.in/demat-account-sitemap.xml
[0m
[0m$ [0mcurl -s -m 15 "https://api-connect-docs.fyers.in/docs/order/" -o /tmp/opencode/order_doc.html 2>&1; grep -oiE "title>[^<]+" /tmp/opencode/order_doc.html | head -2; echo "---endpoint refs---"; grep -oE "(GET|POST|PUT|DELETE) /api/[a-z0-9/_-]*" /tmp/opencode/order_doc.html | sort -u | head; grep -oE "api/v[0-9]/[a-z0-9/_-]*" /tmp/opencode/order_doc.html | sort -u | head -20
title>Order Placement Branded buttons · Fyers
---endpoint refs---
[0m
[0m$ [0mfor sm in others support community; do echo "=== $sm ==="; curl -s -m 20 "https://fyers.in/$sm-sitemap.xml" | grep -oE "<loc>[^<]+" | sed 's/<loc>//' | grep -iE "security|disclos|report|vulnerab|policy|privacy" | head -10; done
=== others ===
=== support ===
https://support.fyers.in/portal/en/kb/articles/what-are-the-fyers-privacy-policies
https://support.fyers.in/portal/en/kb/articles/what-is-disclosed-order-quantity
https://support.fyers.in/portal/en/kb/articles/what-is-disclosed-quantity
https://support.fyers.in/portal/en/kb/articles/what-is-fyers-rms-policy-for-trading
https://support.fyers.in/portal/en/kb/articles/what-is-portfolio-analyzer-report
https://support.fyers.in/portal/en/kb/articles/what-is-the-equity-style-in-the-portfolio-analyser-report
https://support.fyers.in/portal/en/kb/articles/what-is-the-security-commodity-transaction-tax-stt-ctt-at-fyers
https://support.fyers.in/portal/en/kb/articles/where-can-i-read-the-risk-disclosure-document-rdd
https://support.fyers.in/portal/en/kb/platforms-tools/my-account/portfolio-analyzer-report
https://support.fyers.in/portal/en/kb/platforms-tools/my-account/reports
=== community ===
https://fyers.in/community/blogs-gdppin8d/post/chapter-14---annual-report-of-a-company-BNSlSL8JluVanh0
https://fyers.in/community/blogs-gdppin8d/post/chapter-20---global-factors-and-economic-reports-NeKDOogxdFmMlCu
[0m
[0m$ [0mgrep -oE 'href="/docs/[^"]*"' /tmp/opencode/order_doc.html | sort -u; echo "---all links---"; grep -oE 'href="[^"]+"' /tmp/opencode/order_doc.html | grep -vE "css|js|font|img|\.svg|\.png" | sort -u | head -40
href="/docs/custom-page"
href="/docs/funds"
href="/docs/holdings"
href="/docs/hosted-page"
href="/docs/methods"
href="/docs/order"
href="/docs/orders"
href="/docs/parameters"
href="/docs/positions"
href="/docs/trades"
---all links---
href="#basket-order"
href="#buy-order"
href="#install-plugin"
href="#sell-order"
href="/"
href="/demo.html"
href="/docs/custom-page"
href="/docs/funds"
href="/docs/holdings"
href="/docs/hosted-page"
href="/docs/methods"
href="/docs/order"
href="/docs/orders"
href="/docs/parameters"
href="/docs/positions"
href="/docs/trades"
href="https://myapi.fyers.in"
[0m
[0m$ [0mcurl -s -m 20 "https://hackerone.com/fyers" -L -o /tmp/opencode/h1.html 2>&1; grep -oiE "(This site can't be reached|404|fyers\.in|security@|vulnerability)" /tmp/opencode/h1.html | head; wc -c /tmp/opencode/h1.html
2660 /tmp/opencode/h1.html
[0m
[0m$ [0mcurl -s -o /dev/null -w "support.fyers.in -> %{http_code}\n" -m 15 "https://support.fyers.in/"; curl -s -m 15 "https://support.fyers.in/portal/api/" -o /dev/null -w "portal/api -> %{http_code}\n"; curl -s -m 15 "https://support.fyers.in/" | grep -oiE "(title>[^<]+|freshdesk|zendesk|helpshift|hubspot)" | head -5
support.fyers.in -> 301
portal/api -> 404
[0m
[0m$ [0mgrep -oiE "(cannot find|404|no such page|hackerone\.com/[a-z0-9_-]+)" /tmp/opencode/h1.html | head; echo "---"; python3 -c "
import re
d=open('/tmp/opencode/h1.html').read()
print(re.sub(r'\s+',' ',re.sub(r'<[^>]+>',' ',d))[:500])
"
hackerone.com/fyers
---
 HackerOne It looks like your JavaScript is disabled. To use HackerOne, enable JavaScript in your browser and refresh this page.
[0m
[0m$ [0mfor p in methods parameters hosted-page; do echo "=== /docs/$p ==="; curl -s -m 15 -L "https://api-connect-docs.fyers.in/docs/$p/" | grep -oE "api/[a-z0-9/_-]+" | sort -u | head -15; done
=== /docs/methods ===
=== /docs/parameters ===
=== /docs/hosted-page ===
[0m
[0m$ [0mecho "=== full api refs in order doc ==="; grep -oE "https?://[a-z0-9.-]*fyers\.in[^\"' )]*" /tmp/opencode/order_doc.html | sort -u | head -20; echo "=== code blocks ==="; python3 -c "
import re,html
d=open('/tmp/opencode/order_doc.html').read()
for m in re.findall(r'<code[^>]*>(.*?)</code>', d, re.S)[:20]:
    t=re.sub(r'<[^>]+>','',m)
    t=html.unescape(t).strip()
    if any(k in t for k in ['api','http','token','client']): print(t[:200])
"
=== full api refs in order doc ===
https://api-connect-docs.fyers.in/fyers-lib.js
https://assets.fyers.in/global-components/global-footer/footer.js
https://fyers.in/wp-content/plugins/tlp-team/assets/vendor/font-awesome/css/font-awesome.min.css?ver=2.5.2
https://fyers.in/wp-content/themes/salient/css/font-awesome.min.css?ver=4.6.3
https://myapi.fyers.in
=== code blocks ===
<script src="https://api-connect-docs.fyers.in/fyers-lib.js"></script>
<!--A custom button--!>
<button id="custom-button">Buy 2 stocks</button>

<script>
   
    Fyers.ready(function(){

    var fyers = new Fyers("API_KEY")
    
    //add basket of stocks,you can add upt
[0m
[0m$ [0mecho "=== marketdata-api public probes ==="; for p in "" "v1/" "v1/hist" "v1/quote" "v1/tp" "api/" "health" "healthz" "status"; do curl -s -o /dev/null -w "/$p -> %{http_code}\n" -m 12 "https://marketdata-api-instaoptions.fyers.in/$p"; done
=== marketdata-api public probes ===
/ -> 404
/v1/ -> 404
/v1/hist -> 404
/v1/quote -> 404
/v1/tp -> 404
/api/ -> 404
/health -> 200
/healthz -> 404
/status -> 404
[0m
[0m$ [0mecho "=== myapi SPA API endpoints ==="; grep -oE '"/?[a-zA-Z0-9/_.-]{3,60}"' /tmp/opencode/myapi_main2.js | sort -u | grep -iE "api|auth|docs|token|swagger|user|oauth|v[0-9]|link|client|order|market" | head -40
=== myapi SPA API endpoints ===
"-color-deprecated-border"
".share-link"
"/docs"
"/docs/"
"/docsv3"
"/global-components/navbar/user.svg"
"/images/Fyers-Myapi/fyers-api-logo.svg"
"/images/website/icon-link-next.svg"
"/myapi-dashboard/dashboard/close-menu.svg"
"/myapi-dashboard/dashboard/default.svg"
"/myapi-dashboard/dashboard/delete-app-icon.svg"
"/myapi-dashboard/dashboard/down-arrow-active.svg"
"/myapi-dashboard/dashboard/down-arrow-inactive.svg"
"/myapi-dashboard/dashboard/icon-delete.svg"
"/myapi-dashboard/dashboard/icon-edit.svg"
"/myapi-dashboard/dashboard/icon-revoke.svg"
"/myapi-dashboard/dashboard/icon-trash-white.svg"
"/myapi-dashboard/dashboard/image-uplod.svg"
"/myapi-dashboard/dashboard/options.svg"
"/myapi-dashboard/dashboard/revoke-app-icon.svg"
"API_Connect"
"ApiHeader"
"AuthorizationCode"
"ClientCredentials"
"DOCS"
"ExternalDocs"
"FIA_Docs"
"Go_To_API_Dashboard"
"Link"
"LinkMap"
"LinksMap"
"My_API"
"NamedLinks"
"OAuth2"
"OAuth2Flows"
"Read_detailed_API_documentation"
"Unauthorized"
"V3.1_Docs"
"V3_Docs"
"X-XSRF-TOKEN"
[0m
[0m$ [0mcurl -s -m 15 "https://marketdata-api-instaoptions.fyers.in/health" | head -c 400; echo; echo "=== fyers-lib.js ==="; curl -s -m 15 "https://api-connect-docs.fyers.in/fyers-lib.js" | head -c 2000
{"status":"ok","service":"loom","version":"1.0.0"}
=== fyers-lib.js ===
(()=>{"use strict";var A,e={176:(A,e,f)=>{f.d(e,{Z:()=>Z});var n=f(645),v=f.n(n),D=f(667),P=f.n(D),r=f(240),z=f(343),g=f(455),w=f(296),H=f(256),c=f(66),B=f(915),u=f(108),d=f(411),t=f(801),M=f(167),x=f(944),s=f(890),O=f(514),j=v()((function(A){return A[1]})),o=P()(r.Z),X=P()(z.Z),G=P()(g.Z),b=P()(w.Z),h=P()(H.Z),N=P()(c.Z),Y=P()(B.Z),y=P()(u.Z),Q=P()(d.Z),a=P()(t.Z),m=P()(M.Z),i=P()(x.Z),I=P()(s.Z),C=P()(O.Z);j.push([A.id,"\r\n._fyers_button_BUY:hover\r\n{\r\n    \r\n    opacity:0.6;\r\n  \r\n}\r\n._fyers_button_SELL:hover\r\n{\r\n    opacity:0.6\r\n}\r\n._fyers_button_funds:hover\r\n{\r\n    opacity:0.6\r\n}\r\n._fyers_button_positionbook:hover\r\n{\r\n    opacity:0.6\r\n}\r\n._fyers_button_holdings:hover\r\n{\r\n    opacity:0.6\r\n}\r\n._fyers_button_orderbook:hover\r\n{\r\n    opacity:0.6\r\n}\r\n._fyers_button_tradebook:hover\r\n{\r\n    opacity:0.6\r\n}\r\n#custom-button\r\n{\r\n    width: 12%;\r\n    height: 40px;\r\n}\r\n.custom_button\r\n{\r\n    width:100%;\r\n    float:left;\r\n    height:100%;\r\n}\r\n.button_image\r\n{\r\n    \r\n    width: 20%;\r\n    float: left;\r\n    height:100%\r\n}\r\n.placeholder\r\n{\r\n    text-align: center;\r\n    color:#fff;\r\n    padding-top:6%;\r\n    width:60%;\r\n    float: left;\r\n\r\n}\r\n._fyers_button_BUY\r\n{\r\n    background: url("+o+");\r\n    background-repeat: no-repeat;\r\n    width: 270px;\r\n    float: left;\r\n    height: 105px;\r\n    cursor: pointer;\r\n\r\n}\r\n\r\n._fyers_button_SELL\r\n{\r\n    background: url("+X+");\r\n    background-repeat: no-repeat;\r\n    width: 270px;\r\n    float: left;\r\n    height: 105px;\r\n    cursor: pointer;\r\n\r\n}\r\n._fyers_button_funds\r\n{\r\n    background: url("+G+");\r\n    background-repeat: no-repeat;\r\n    width: 270px;\r\n    float: left;\r\n    height: 105px;\r\n    cursor: pointer; \r\n}\r\n._fyers_button\r\n{\r\n    background-color: #1b97f3;\r\n    height: 45px;\r\n    width: 25%;\r\n    float: left;\r\n    border-radius: 6px;\r\n    cursor:pointer;\r\
[0m
[0m$ [0mgrep -oE "(myapi|fyers-api|api-dashboard)[a-zA-Z0-9/_.-]*" /tmp/opencode/myapi_main2.js | sort -u | head; echo "=== fetch/axios url patterns ==="; grep -oE '"[a-zA-Z0-9/_.-]*api[a-zA-Z0-9/_.-]{3,50}"' /tmp/opencode/myapi_main2.js | sort -u | head -40
api-dashboard/user-apps
fyers-api-c.svg
fyers-api-go.svg
fyers-api-logo.svg
fyers-api-rha0riqv
fyers-api.svg
myapi-dashboard/dashboard/close-menu.svg
myapi-dashboard/dashboard/default.svg
myapi-dashboard/dashboard/delete-app-icon.svg
myapi-dashboard/dashboard/down-arrow-active.svg
=== fetch/axios url patterns ===
"/images/Fyers-Myapi/fyers-api-logo.svg"
"/myapi-dashboard/dashboard/close-menu.svg"
"/myapi-dashboard/dashboard/default.svg"
"/myapi-dashboard/dashboard/delete-app-icon.svg"
"/myapi-dashboard/dashboard/down-arrow-active.svg"
"/myapi-dashboard/dashboard/down-arrow-inactive.svg"
"/myapi-dashboard/dashboard/icon-delete.svg"
"/myapi-dashboard/dashboard/icon-edit.svg"
"/myapi-dashboard/dashboard/icon-revoke.svg"
"/myapi-dashboard/dashboard/icon-trash-white.svg"
"/myapi-dashboard/dashboard/image-uplod.svg"
"/myapi-dashboard/dashboard/options.svg"
"/myapi-dashboard/dashboard/revoke-app-icon.svg"
"api-content"
"api-info"
"apiDefinitions"
"apiKey"
"apiList"
"autocapitalize"
"openapi.json"
"x-amazon-apigateway-any-method"
[0m
[0m$ [0mgrep -oE "https?://[a-z0-9.-]*fyers\.in[^\"' ]*" /tmp/opencode/myapi_main2.js | sort -u | head -40; echo "=== fia ==="; grep -oE ".{60}FIA.{60}" /tmp/opencode/myapi_main2.js | head -5
https://alerts.fyers.in
https://api-connect-docs.fyers.in/
https://api-t1.fyers.in/api/v2/user
https://api-t1.fyers.in/gk/verify_token
https://assets.fyers.in
https://assets.fyers.in/Marcom_Avatar/Images/fyers-api-c.svg
https://assets.fyers.in/Marcom_Avatar/Images/fyers-api-go.svg
https://assets.fyers.in/images/Fyers-Myapi/api-connect.svg
https://assets.fyers.in/images/Fyers-Myapi/icon-algorthmic-trading.svg
https://assets.fyers.in/images/Fyers-Myapi/icon-free.svg
https://assets.fyers.in/images/Fyers-Myapi/icon-platforms.svg
https://assets.fyers.in/images/Fyers-Myapi/icon-robust.svg
https://assets.fyers.in/images/Fyers-Myapi/icon-simple.svg
https://assets.fyers.in/images/Fyers-Myapi/icon-startups.svg
https://assets.fyers.in/images/Fyers-Myapi/illustration-corporate.svg
https://assets.fyers.in/images/Fyers-Myapi/node-js.svg
https://assets.fyers.in/images/Fyers-Myapi/python.svg
https://assets.fyers.in/images/Fyers-Myapi/rest.svg
https://assets.fyers.in/images/global-header/fyers-pledge-icon.svg
https://assets.fyers.in/images/pledge/logo-holiday-calender.svg
https://assets.fyers.in/images/pledge/logo-insight.svg
https://assets.fyers.in/images/pledge/logo-noticeboard.svg
https://assets.fyers.in/images/pledge/logo-sos.svg
https://assets.fyers.in/images/website/Fyers-sgb-icon.svg
https://assets.fyers.in/images/website/api/integration-methods/c-sharp.svg
https://assets.fyers.in/images/website/api/integration-methods/java.svg
https://assets.fyers.in/images/website/api/integration-methods/js.svg
https://assets.fyers.in/images/website/icon-brokerage-calculator.svg
https://assets.fyers.in/images/website/icon-fyers-api.svg
https://assets.fyers.in/images/website/icon-fyers-app.svg
https://assets.fyers.in/images/website/icon-fyers-direct.svg
https://assets.fyers.in/images/website/icon-fyers-one.svg
https://assets.fyers.in/images/website/icon-fyers-web.svg
https://assets.fyers.in/images/website/icon-ipo.svg
https://assets.fyers.in/images/website/icon-link-next.svg
https://assets.fyers.in/images/website/icon-margin-calculator.svg
https://assets.fyers.in/images/website/icon-ofs.svg
https://assets.fyers.in/images/website/icon-price-alerts.svg
https://community.fyers.in/fyers-api-rha0riqv
https://direct.fyers.in
=== fia ===
t(t&&"active")},onClick:function(){return window.analytics("FIA_Docs",gf.google_analytics_event_category,gf.google_analytic
m.jsx)("li",{children:(0,Gm.jsx)(an,{to:"docsfia",children:"FIA"})}),(0,Gm.jsx)("li",{children:(0,Gm.jsx)("a",{href:"https:
[0m
[0m$ [0mfor p in openapi.json docs docsv3 docsfia api-dashboard api-dashboard/user-apps swagger.json api/v3/definitions; do curl -s -o /dev/null -w "/$p -> %{http_code} %{content_type}\n" -m 12 "https://myapi.fyers.in/$p"; done
/openapi.json -> 404 text/html
/docs -> 404 text/html
/docsv3 -> 404 text/html
/docsfia -> 404 text/html
/api-dashboard -> 404 text/html
/api-dashboard/user-apps -> 404 text/html
/swagger.json -> 404 text/html
/api/v3/definitions -> 404 text/html
[0m
[0m$ [0mecho "=== context around user-apps ==="; grep -oE ".{120}user-apps.{120}" /tmp/opencode/myapi_main2.js | head -3; echo "=== context around openapi.json ==="; grep -oE ".{100}openapi\.json.{100}" /tmp/opencode/myapi_main2.js | head -5
=== context around user-apps ===
ard",gf.google_analytics_event_category,gf.google_analytics_event_type),window.open("https://fyers.in/web/api-dashboard/user-apps","_blank","noopener,noreferrer")}return(0,t.useEffect)((function(){n.loggedIn||function(){r.apply(this,arguments)}()}),
=== context around openapi.json ===
l||this.options.downloadDefinitionUrl?this.options.downloadFileName:this.options.downloadFileName||"openapi.json"}}]),e}(),ua=Object.defineProperty,la=Object.defineProperties,ca=Object.getOwnPropertyDescriptors,f
[0m
[0m$ [0mecho "=== api-t1 known v3 endpoints via OPTIONS ==="; for p in validate-authcode auth/token orders order positions funds trades holdings market-status profile logout; do printf "%-22s " "$p"; curl -s -o /dev/null -w "%{http_code}\n" -m 12 -X POST "https://api-t1.fyers.in/api/v3/$p"; done
=== api-t1 known v3 endpoints via OPTIONS ===
validate-authcode      400
auth/token             404
orders                 403
order                  404
positions              401
funds                  403
trades                 404
holdings               403
market-status          404
profile                403
logout                 400
[0m
[0m$ [0mfor h in partners.fyers.in instantpayout.fyers.in workdrive.fyers.in learn.fyers.in projects.fyers.in supportdesk.fyers.in; do echo "=== $h ==="; curl -s -m 15 -D - -o /dev/null "https://$h/" 2>&1 | grep -iE "^(HTTP|server|location|x-powered|via)" | head -4; done
=== partners.fyers.in ===
HTTP/2 200 
server: cloudflare
=== instantpayout.fyers.in ===
=== workdrive.fyers.in ===
HTTP/2 302 
server: ZGS
location: https://workdrive.zoho.in/login.jsp
=== learn.fyers.in ===
HTTP/2 200 
server: cloudflare
=== projects.fyers.in ===
HTTP/1.1 302 
Server: ZGS
Location: https://accounts.zoho.in/signin?servicename=null&serviceurl=https%3A%2F%2Fprojects.zoho.in
=== supportdesk.fyers.in ===
HTTP/2 301 
server: cloudflare
[0m
[0m$ [0mecho "=== web api-dashboard ==="; curl -s -m 15 -D - -o /dev/null "https://fyers.in/web/api-dashboard/user-apps" 2>&1 | grep -iE "^(HTTP|location|server)"; echo "=== web main.js refs ==="; grep -oE 'https://[a-z0-9.-]*fyers\.in/[a-zA-Z0-9/_-]*' /tmp/opencode/web.html | sort -u | head
=== web api-dashboard ===
HTTP/2 200 
server: cloudflare
=== web main.js refs ===
https://assets.fyers.in/discover/icons/favicon/Favicon
https://assets.fyers.in/images/fyers_meta
https://fyers.in/web/
[0m
[0m$ [0mecho "=== validate-authcode error shapes ==="; curl -s -m 15 -X POST -H "Content-Type: application/json" -d '{}' "https://api-t1.fyers.in/api/v3/validate-authcode" | head -c 300; echo; echo "=== validate-refresh-token ==="; curl -s -m 15 -X POST -H "Content-Type: application/json" -d '{}' "https://api-t1.fyers.in/api/v3/validate-refresh-token" | head -c 300; echo; echo "=== logout ==="; curl -s -m 15 -X POST -H "Content-Type: application/json" -d '{}' "https://api-t1.fyers.in/api/v3/logout" | head -c 300
=== validate-authcode error shapes ===
{"code":-441,"message":"auth code is required","s":"error"}
=== validate-refresh-token ===
{"code":-442,"message":"Please provide valid grant_type","s":"error"}
=== logout ===
{"s":"error","code":-16,"message":"Token is Invalid or Expired"}
[0m
[0m$ [0mfor h in learn.fyers.in partners.fyers.in; do echo "=== $h ==="; curl -s -m 15 "https://$h/" | grep -oiE "<title>[^<]+|framer|wordpress|next\.js|react|angular|shopify" | head -3; done
=== learn.fyers.in ===
<title>Page not found
=== partners.fyers.in ===
<title>Partners Dashboard 3.0 - A New and Enhanced Fyers Partners Dashboard
[0m
[0m$ [0mecho "=== v2 user endpoint ==="; curl -s -m 15 -X POST -H "Content-Type: application/json" -d '{}' "https://api-t1.fyers.in/api/v2/user" | head -c 300; echo; echo "=== v2 validate-refresh-token grant_type enum ==="; for gt in authorization_code refresh_token client_credentials app_token invalid; do printf "%-20s " "$gt"; curl -s -m 12 -X POST -H "Content-Type: application/json" -d "{\"grant_type\":\"$gt\"}" "https://api-t1.fyers.in/api/v2/validate-refresh-token" | head -c 120; echo; done
=== v2 user endpoint ===

=== v2 validate-refresh-token grant_type enum ===
authorization_code   
refresh_token        
client_credentials   
app_token            
invalid
[0m
[0m$ [0mfor gt in authorization_code refresh_token client_credentials app_token; do printf "%-20s " "$gt"; curl -s -m 12 -X POST -H "Content-Type: application/json" -d "{\"grant_type\":\"$gt\"}" "https://api.fyers.in/api/v2/validate-refresh-token" | head -c 140; echo; done
authorization_code   {"s": "error", "code": -442, "message": "Please provide valid grant_type"}
refresh_token        {"s": "error", "code": -501, "message": "Please provide valid refresh token"}
client_credentials   {"s": "error", "code": -442, "message": "Please provide valid grant_type"}
app_token            {"s": "error", "code": -442, "message": "Please provide valid grant_type"}
[0m
[0m$ [0mecho "=== myapi data fetch patterns ==="; grep -oE 'fetch\("[^"]+"|axios\.[a-z]+\(|apiBase|baseURL[^,;]{0,60}|endpoint[^,;]{0,60}' /tmp/opencode/myapi_main2.js | sort -u | head -30; echo "=== doc content paths ==="; grep -oE '"[a-z0-9/._-]*doc[a-z0-9/._-]*"' /tmp/opencode/myapi_main2.js | grep -vE "\.svg|\.css" | sort -u | head -30
=== myapi data fetch patterns ===
baseURL
baseURL:s
endpoint}"}
=== doc content paths ===
".redocly-config.json"
".redocly.host"
".redocly.lint-ignore.yaml"
"/docs"
"/docs/"
"/docsv3"
"__redoc_param_name__"
"doc"
"docs"
"docsfia"
"docsv3"
"doctype-tag"
"eu.redocly.com"
"heredoc-string"
"nowdoc-string"
"redoc-description"
"redoc-json"
"redoc-markdown"
"redoc-summary"
"redoc-wrap"
"redoc.ly"
"redoc.online"
"redocly.com"
"x-redocly-cli-version"
[0m
[0m$ [0mgrep -oE '"/[a-z0-9/_{}.-]+"' /tmp/opencode/myapi_main2.js | grep -E "v2|v3|auth|quote|order|trade|fund|position|hold|user|token|profile|market|history|symbol" | sort -u | head -40
"/docsv3"
"/global-components/navbar/user.svg"
[0m
[0m$ [0mgrep -oE '"[a-zA-Z0-9/._-]*(yaml|json)[a-zA-Z0-9/._-]*"' /tmp/opencode/myapi_main2.js | sort -u | head -30; echo "=== spec-load context ==="; grep -oE '.{80}(specUrl|spec-url|getSpec|loadSpec|definitionUrl).{80}' /tmp/opencode/myapi_main2.js | head -6
".json"
".redocly-config.json"
".redocly.lint-ignore.yaml"
".yaml"
"/json/pointer"
"1/relative/json/pointer"
"application/json"
"json"
"json-pointer"
"jsonLike"
"openapi.json"
"redoc-json"
"relative-json-pointer"
"static/media/fia.9dcf545bc3f508df4db9.yaml"
"static/media/v3.1.32a8eeba1fba866d1201.yaml"
"static/media/v3.fc0a0244d7d288c81e4f.yaml"
"yaml-1.1"
=== spec-load context ===
"Event",const:"Value",noResultsFound:"No results found",download:"Download",downloadSpecification:"Download OpenAPI specification",responses:"Responses",callbackRespons
.downloadDefinitionUrl)return this.options.downloadDefinitionUrl;if(this.parser.specUrl)return this.parser.specUrl;if(kn&&window.Blob&&window.URL&&window.URL.createObj
eObjectURL(e)}}},{key:"getDownloadFileName",value:function(){return this.parser.specUrl||this.options.downloadDefinitionUrl?this.options.downloadFileName:this.options.
pi.startsWith("3.1");var o=kn?window.location.href:"";"string"==typeof n&&(this.specUrl=o?new URL(n,o).href:n)}return vn(e,[{key:"validate",value:function(e){if(void 0
o.description,o.externalValue&&(this.externalValueUrl=new URL(o.externalValue,t.specUrl).href),ai(r)&&this.value&&"object"==typeof this.value&&(this.value=ci(this.valu
||t.spec.security||[]).map((function(e){return new ma(e,t)})),this.servers=_i(t.specUrl,n.servers||n.pathServers||t.spec.servers||[])),i.showExtensions&&(this.extensio
[0m
[0m$ [0mcd /tmp/opencode; for f in "v3.fc0a0244d7d288c81e4f.yaml:v3.yaml" "v3.1.32a8eeba1fba866d1201.yaml:v3_1.yaml" "fia.9dcf545bc3f508df4db9.yaml:fia.yaml"; do url="https://myapi.fyers.in/static/media/${f%%:*}"; out="${f##*:}"; curl -s -m 30 "$url" -o "$out"; echo "$out -> $(wc -c < $out) bytes"; done
v3.yaml -> 1576028 bytes
v3_1.yaml -> 4703 bytes
fia.yaml -> 6194 bytes
[0m
[0m$ [0mcd /tmp/opencode; echo "=== v3 spec servers/info/security ==="; python3 -c "
import yaml
d=yaml.safe_load(open('v3.yaml'))
print('TITLE:', d.get('info',{}).get('title'), '| VERSION:', d.get('info',{}).get('version'))
print('SERVERS:', d.get('servers'))
print('SCHEMES:', list((d.get('components',{}).get('securitySchemes') or {}).keys()))
print('GLOBAL SEC:', d.get('security'))
print('TOTAL PATHS:', len(d.get('paths',{})))
"
=== v3 spec servers/info/security ===
TITLE: FYERS APIS | VERSION: None
SERVERS: [{'url': '//petstore.swagger.io/v2', 'description': 'Default server'}, {'url': '//petstore.swagger.io/sandbox', 'description': 'Sandbox server'}]
SCHEMES: []
GLOBAL SEC: None
TOTAL PATHS: 51
[0m
[0m$ [0mcd /tmp/opencode; python3 -c "
import yaml
d=yaml.safe_load(open('v3.yaml'))
for p,ops in d['paths'].items():
    for m,o in ops.items():
        if m.lower() in ('get','post','put','delete','patch'):
            print(f'{m.upper():6s} {p:45s} tags={o.get(\"tags\",[\"\"])[0] if o.get(\"tags\") else \"\"} | {o.get(\"summary\",\"\")[:60]}')
"
POST   /ThirdParty                                   tags=Authentication & Login Flow - Third Party Apps | OAuth2 - Auth Flow
PUT    /ThirdParty                                   tags=Authentication & Login Flow - Third Party Apps | Best Practices
POST   /Auth                                         tags=Request & Response Structure | Authorization Headers
PATCH  /Auth                                         tags=Request & Response Structure | Success
DELETE /Auth                                         tags=Request & Response Structure | Failure
GET    /pet/{petId}                                  tags=Request & Response Structure | HTTP Status Codes
PATCH  /pet/{petId}                                  tags=Request & Response Structure | Common API Error Codes
POST   /pet/{petId}                                  tags=Request & Response Structure | Rate Limits
DELETE /pet/{petId}                                  tags=Request & Response Structure | Permission Templates
PUT    /pet/{petId}                                  tags=Request & Response Structure | User blocking
POST   /MarginCalculator                             tags=Margin Calculator | Span Margin Calculator
GET    /MarginCalculator                             tags=Margin Calculator | Multiorder Margin Calculator
POST   /GTT Orders                                   tags=GTT Orders | GTT Single
PUT    /GTT Orders                                   tags=GTT Orders | GTT OCO
PATCH  /GTT Orders                                   tags=GTT Orders | GTT Modify Order
GET    /GTT Orders                                   tags=GTT Orders | GTT Cancel Order
POST   /SmartOrders/limit                            tags=Smart Orders | Smart Limit Order
POST   /SmartOrders/trail                            tags=Smart Orders | Smart Trail (Trailing Stop Loss)
POST   /SmartOrders/step                             tags=Smart Orders | Smart Step Order
POST   /SmartOrders/sip                              tags=Smart Orders | Smart SIP (Systematic Investment Plan)
PATCH  /SmartOrders/modify                           tags=Smart Orders | Modify Smart Order
DELETE /SmartOrders/cancel                           tags=Smart Orders | Cancel Smart Order
PATCH  /SmartOrders/pause                            tags=Smart Orders | Pause Smart Order
PATCH  /SmartOrders/resume                           tags=Smart Orders | Resume Smart Order
GET    /SmartOrders/orderbook                        tags=Smart Orders | Smart Order Book
POST   /GeneralSocket                                tags=Web Socket | General Socket (orders)
GET    /GeneralSocket                                tags=Web Socket | General Socket (trades)
PUT    /GeneralSocket                                tags=Web Socket | General Socket (positions)
PATCH  /GeneralSocket                                tags=Web Socket | General Socket (general)
GET    /WebSocket                                    tags=Web Socket | Market Data Symbol Update
PATCH  /WebSocket                                    tags=Web Socket | Market Data Lite-Mode
PUT    /WebSocket                                    tags=Web Socket | Market Data Unsubscribe
DELETE /WebSocket                                    tags=Web Socket | Advanced Configuration
POST   /OrderWebsocketUsageGuide                     tags=Order Websocket Usage Guide | Order WebSocket Connection
GET    /OrderWebsocketUsageGuide                     tags=Order Websocket Usage Guide | Subscribe Method
PUT    /OrderWebsocketUsageGuide                     tags=Order Websocket Usage Guide | UnSubscribe Method
PATCH  /OrderWebsocketUsageGuide                     tags=Order Websocket Usage Guide | Ping Method
POST   /TBTWebsocketUsageGuide                       tags=Tbtws | TBT WebSocket Connection [50 Market Depth]
PUT    /TBTWebsocketUsageGuide                       tags=Tbtws | Concept of channels
GET    /TBTWebsocketUsageGuide                       tags=Tbtws | Request Message Types
PATCH  /TBTWebsocketUsageGuide                       tags=Tbtws | Response Message Types
DELETE /TBTWebsocketUsageGuide                       tags=Tbtws | Ratelimits
POST   /EDIS                                         tags=EDIS | TPIN Generation
GET    /EDIS                                         tags=EDIS | Details
PUT    /EDIS                                         tags=EDIS | Index
DELETE /EDIS                                         tags=EDIS | Inquiry
POST   /Broker Config                                tags=Broker Config | Market Status
PATCH  /Broker Config                                tags=Broker Config | Symbol Master
PUT    /Broker Config                                tags=Broker Config | Symbol Master Json
POST   /OtherTransactions                            tags=Other Transactions | Modify Orders
GET    /OtherTransactions                            tags=Other Transactions | Modify Multi Orders
PUT    /OtherTrans                                   tags=Other Transactions | Cancel Order
DELETE /OtherTrans                                   tags=Other Transactions | Cancel Multi Order
PATCH  /OtherTrans                                   tags=Manage Positions | Exit Position
GET    /OtherTrans                                   tags=Other Transactions | Pending Order Cancel
GET    /OthePlacement                                tags=Sync Order Placement | Single Order
PATCH  /OthePlacement                                tags=Sync Order Placement | Multi Order
PUT    /OthePlacement                                tags=Sync Order Placement | MultiLeg Order
POST   /OthePlacement                                tags=Manage Positions | Convert Position
POST   /ManagePositions                              tags=Manage Positions | Set stop-loss and target
PATCH  /ManagePositions                              tags=Manage Positions | Modify stop-loss and target
GET    /AsyncOrderPlacement                          tags=Async Order Placement | Overview - Sync and Async APIs
POST   /AsyncOrderPlacement                          tags=Async Order Placement | Place order (async)
PATCH  /AsyncOrderPlacement                          tags=Async Order Placement | Modify order (async)
DELETE /AsyncOrderPlacement                          tags=Async Order Placement | Cancel order (async)
GET    /AsyncMultiOrderPlacement                     tags=Async Order Placement | Multi-order async — queue and response pattern
POST   /AsyncMultiOrderPlacement                     tags=Async Order Placement | Place multiple orders (async)
PATCH  /AsyncMultiOrderPlacement                     tags=Async Order Placement | Modify multiple orders (async)
DELETE /AsyncMultiOrderPlacement                     tags=Async Order Placement | Cancel multiple orders (async)
POST   /DataApi                                      tags=Data Api | History
GET    /DataApi                                      tags=Data Api | Quotes
PUT    /DataApi                                      tags=Data Api | Market Depth
DELETE /DataApi                                      tags=Data Api | Option Chain
POST   /PriceAlerts                                  tags=Price Alerts | Create Price Alert
GET    /PriceAlerts                                  tags=Price Alerts | Get Price Alerts
PUT    /PriceAlerts                                  tags=Price Alerts | Modify Price Alert
DELETE /PriceAlerts                                  tags=Price Alerts | Delete Price Alert
PUT    /Enable Disable Price Alert                   tags=Price Alerts | Enable/Disable Price Alert
GET    /Enable Disable Price Alert                   tags=Transaction Info | Trades
POST   /Authentication & Login Flow - User Apps      tags=Authentication & Login Flow - User Apps | Authentication Steps
PATCH  /Authentication & Login Flow - User Apps      tags=Authentication & Login Flow - User Apps | Request Parameters for Step 1
GET    /Authentication & Login Flow - User Apps      tags=Authentication & Login Flow - User Apps | Request Parameters for Step 2
DELETE /Authentication & Login Flow - User Apps      tags=Authentication & Login Flow - User Apps | Refresh Token
PUT    /Authentication & Login Flow - User Apps      tags=Authentication & Login Flow - User Apps | Best Practices
DELETE /User                                         tags=User | FyersModel Class (Python SDK)
POST   /User                                         tags=User | Profile
PUT    /User                                         tags=User | Funds
GET    /User                                         tags=User | Holdings
PATCH  /User                                         tags=User | Logout
POST   /Transaction Info                             tags=Transaction Info | Orders
PUT    /Transaction Info                             tags=Transaction Info | Order Filter By Order Id
PATCH  /Transaction Info                             tags=Transaction Info | Positions
GET    /Reports/Order History                        tags=Reports | Order History
GET    /Reports/Trade History                        tags=Reports | Trade History
GET    /Reports/Charges History                      tags=Reports | Charges History
GET    /Reports/Realised Profit & Loss History       tags=Reports | Realised Profit & Loss
GET    /Reports/Tax Profit & Loss History            tags=Reports | Tax Profit & Loss
GET    /Reports/Ledger History                       tags=Reports | Ledger
POST   /pet/{petId}/uploadImage                      tags=pet | uploads an image
GET    /pet/findByStatus                             tags=pet | Finds pets by status
GET    /pet/findByTags                               tags=pet | Finds pets by tags
GET    /store/inventory                              tags=store | Returns pet inventories by status
POST   /store/order                                  tags=store | Place an order for a pet
GET    /store/order/{orderId}                        tags=store | Find purchase order by ID
DELETE /store/order/{orderId}                        tags=store | Delete purchase order by ID
POST   /store/subscribe                              tags=store | Subscribe to the Store events
POST   /user                                         tags=user | Create user
GET    /user/{username}                              tags=user | Get user by user name
PUT    /user/{username}                              tags=user | Updated user
DELETE /user/{username}                              tags=user | Delete user
POST   /user/createWithArray                         tags=user | Creates list of users with given input array
POST   /user/createWithList                          tags=user | Creates list of users with given input array
GET    /user/login                                   tags=user | Logs user into the system
GET    /user/logout                                  tags=user | Logs out current logged in user session
[0m
[0m$ [0mcd /tmp/opencode; echo "=== real API URLs inside spec ==="; grep -oE "https://[a-z0-9.-]*fyers\.in[a-zA-Z0-9/_-]*" v3.yaml | sort -u | head -30; echo "=== auth/security schemes ==="; grep -oE "(bearer|Bearer|OAuth|oAuth|X-App-Id|appIdHash|x-app-id|x-auth)" v3.yaml | sort | uniq -c | sort -rn | head
=== real API URLs inside spec ===
https://api-t1.fyers.in
https://api-t1.fyers.in/api/v3/charges-history
https://api-t1.fyers.in/api/v3/flows/tc/se
https://api-t1.fyers.in/api/v3/flows/tc/se/activate
https://api-t1.fyers.in/api/v3/funds
https://api-t1.fyers.in/api/v3/generate-authcode
https://api-t1.fyers.in/api/v3/gtt/orders
https://api-t1.fyers.in/api/v3/gtt/orders/sync
https://api-t1.fyers.in/api/v3/holdings
https://api-t1.fyers.in/api/v3/ledger-history
https://api-t1.fyers.in/api/v3/logout
https://api-t1.fyers.in/api/v3/multi-order/async
https://api-t1.fyers.in/api/v3/multi-order/sync
https://api-t1.fyers.in/api/v3/multileg/orders/sync
https://api-t1.fyers.in/api/v3/multiorder/margin
https://api-t1.fyers.in/api/v3/order-history
https://api-t1.fyers.in/api/v3/orders
https://api-t1.fyers.in/api/v3/orders/52009227353/sync
https://api-t1.fyers.in/api/v3/orders/async
https://api-t1.fyers.in/api/v3/orders/sync
https://api-t1.fyers.in/api/v3/positions
https://api-t1.fyers.in/api/v3/price-alert
https://api-t1.fyers.in/api/v3/profile
https://api-t1.fyers.in/api/v3/realised-pnl-history
https://api-t1.fyers.in/api/v3/smart-order/cancel
https://api-t1.fyers.in/api/v3/smart-order/limit
https://api-t1.fyers.in/api/v3/smart-order/modify
https://api-t1.fyers.in/api/v3/smart-order/orderbook
https://api-t1.fyers.in/api/v3/smart-order/pause
https://api-t1.fyers.in/api/v3/smart-order/resume
=== auth/security schemes ===
      7 appIdHash
      6 OAuth
[0m
[0m$ [0mcd /tmp/opencode; echo "=== ALL real API URLs (full) ==="; grep -oE "https://api-t1\.fyers\.in[a-zA-Z0-9/_{}.?=&-]*" v3.yaml | sort -u
=== ALL real API URLs (full) ===
https://api-t1.fyers.in
https://api-t1.fyers.in/api/v3/charges-history?exchange_type=0&from_date=2026-04-01&to_date=2027-03-31&page_no=1&page_size=1&segment_type=0
https://api-t1.fyers.in/api/v3/flows/tc/se
https://api-t1.fyers.in/api/v3/flows/tc/se/activate
https://api-t1.fyers.in/api/v3/funds
https://api-t1.fyers.in/api/v3/generate-authcode?
https://api-t1.fyers.in/api/v3/generate-authcode?client_id=SPXXXXE7-100&redirect_uri=https
https://api-t1.fyers.in/api/v3/gtt/orders
https://api-t1.fyers.in/api/v3/gtt/orders/sync
https://api-t1.fyers.in/api/v3/holdings
https://api-t1.fyers.in/api/v3/ledger-history?page_size=1
https://api-t1.fyers.in/api/v3/logout
https://api-t1.fyers.in/api/v3/multi-order/async
https://api-t1.fyers.in/api/v3/multi-order/sync
https://api-t1.fyers.in/api/v3/multileg/orders/sync
https://api-t1.fyers.in/api/v3/multiorder/margin
https://api-t1.fyers.in/api/v3/order-history?symbol=INFIBEAM&page_size=100&page_no=1&from_date=2025-04-01&to_date=2025-12-22&segment_type=0&exchange_type=0&status=0
https://api-t1.fyers.in/api/v3/orders
https://api-t1.fyers.in/api/v3/orders/52009227353/sync
https://api-t1.fyers.in/api/v3/orders/async
https://api-t1.fyers.in/api/v3/orders/sync
https://api-t1.fyers.in/api/v3/orders??order_tag=1
https://api-t1.fyers.in/api/v3/orders?id=sample_order_id
https://api-t1.fyers.in/api/v3/positions
https://api-t1.fyers.in/api/v3/price-alert
https://api-t1.fyers.in/api/v3/profile
https://api-t1.fyers.in/api/v3/realised-pnl-history?exchange_type=0&from_date=2026-04-01&to_date=2027-03-31&page_no=1&page_size=10&segment_type=0&symbol=NSE
https://api-t1.fyers.in/api/v3/smart-order/cancel
https://api-t1.fyers.in/api/v3/smart-order/limit
https://api-t1.fyers.in/api/v3/smart-order/modify
https://api-t1.fyers.in/api/v3/smart-order/orderbook?page_no=1&page_size=15
https://api-t1.fyers.in/api/v3/smart-order/pause
https://api-t1.fyers.in/api/v3/smart-order/resume
https://api-t1.fyers.in/api/v3/smart-order/sip
https://api-t1.fyers.in/api/v3/smart-order/step
https://api-t1.fyers.in/api/v3/smart-order/trail
https://api-t1.fyers.in/api/v3/tax-pnl-history?fin_year=2026&transaction_type=0&page_size=1
https://api-t1.fyers.in/api/v3/toggle-alert
https://api-t1.fyers.in/api/v3/trade-history?exchange_type=0&from_date=2025-04-01&to_date=2025-12-22&page_no=1&page_size=10&segment_type=0&symbol=NSE
https://api-t1.fyers.in/api/v3/tradebook
https://api-t1.fyers.in/api/v3/tradebook?order_tag=1
https://api-t1.fyers.in/api/v3/validate-authcode
https://api-t1.fyers.in/api/v3/validate-refresh-token
https://api-t1.fyers.in/data/depth
https://api-t1.fyers.in/data/depth?symbol=NSE
https://api-t1.fyers.in/data/history
https://api-t1.fyers.in/data/history?symbol=NSE
https://api-t1.fyers.in/data/marketStatus
https://api-t1.fyers.in/data/options-chain-v3?symbol=NSE
https://api-t1.fyers.in/data/quotes
https://api-t1.fyers.in/data/quotes?symbols=NSE
[0m
[0m$ [0mcd /tmp/opencode; echo "=== fia.yaml ==="; python3 -c "
import yaml
d=yaml.safe_load(open('fia.yaml'))
print('TITLE:', d.get('info',{}).get('title'), '| DESC:', str(d.get('info',{}).get('description'))[:200])
print('PATHS:', list(d.get('paths',{}).keys()))
"; echo "=== v3_1.yaml ==="; python3 -c "
import yaml
d=yaml.safe_load(open('v3_1.yaml'))
print('TITLE:', d.get('info',{}).get('title'))
print('PATHS:', list(d.get('paths',{}).keys()))
print('DESC:', str(d.get('info',{}).get('description'))[:300])
"
=== fia.yaml ===
TITLE: None | DESC: 
# Introduction

FYERS MCP (Model Context Protocol) is the easiest way to sync your FYERS account with your AI platform of choice like Claude, Cursor, Windsurf and more.
With one-tap installation.

Yo
PATHS: []
=== v3_1.yaml ===
TITLE: Regulatory Changes (April 2026)
PATHS: []
DESC: As per the new retail algorithmic trading framework introduced by the Securities and Exchange Board of India (SEBI), the following changes will apply to order placement via the FYERS API.

**All API users must update their integration before March 31, 2026 to avoid disruption in order placement.
For
[0m
[0m$ [0mcd /tmp/opencode; echo "=== auth header / appIdHash context ==="; grep -oE ".{100}appIdHash.{150}" v3.yaml | head -3; echo "=== generate-authcode example ==="; grep -oE ".{80}generate-authcode.{200}" v3.yaml | head -4
=== auth header / appIdHash context ===
=== generate-authcode example ===
[0m
[0m$ [0mcd /tmp/opencode; echo "=== FIA/MCP full text ==="; python3 -c "
import yaml
d=yaml.safe_load(open('fia.yaml'))
print(d.get('info',{}).get('description',''))" | head -60
=== FIA/MCP full text ===

# Introduction

FYERS MCP (Model Context Protocol) is the easiest way to sync your FYERS account with your AI platform of choice like Claude, Cursor, Windsurf and more.
With one-tap installation.

Your assistant instantly understands your trading context stocks, mutual funds, positions, orders, GTTs, option chains, alerts, live and historic data.

No generic answers. No setup hassle. Just context-aware insights, built on your FYERS data. Tailor-made for expert traders and investors. 

# What is FYERS MCP?

Every AI tool is only as good as the context it has. FYERS MCP injects your portfolio, trades, and market data directly into your assistant, so it can respond with answers personalised to you.

For traders and investors, this means natural-language queries like “What’s my P&L this week?” or “Show me my exposure in banking stocks.”

For developers, this means direct access to a raw MCP server schema—so you can integrate, extend, and build custom workflows on top of FYERS.

MCPs are your way to sync FYERS context into your AI, making every response sharper, faster, and more useful.

# Why FYERS MCP is Better

Most MCPs are clunky, limited, and built only for developers. **FYERS MCP** is built for *everyone*—giving you all the power others offer, plus more.

| Feature / Experience | FYERS MCP 🚀 | Others ❌ |
|-----------------------|--------------|-----------|
| **Installation**      | One-tap (.exe / .pkg) | Multi-step, config heavy |
| **Ease of Use**       | Simple, no coding needed | Geared towards devs only |
| **Market Data**       | Live + Historic prices | Often live only |
| **Portfolio Coverage** | Stocks, MFs, trades, orders, GTT, alerts | Limited holdings/trades |
| **Options & F&O**     | Full option chain | Partial or missing |
| **Developer Value**   | Raw MCP schema + server specs to build custom tools | MCP Server Schema and Specs |
| **Audience**          | Investors + Developers | Mostly developers |
| **Coverage**          | Equities, Derivatives, MFs | Equities, Derivatives, MFs |

---

With **FYERS MCP**, you’re not choosing between ease and depth—you get both.

# How to Install

FYERS MCP is packaged for **Mac (.pkg)** and **Windows (.exe)**. Just download, run, log in, and authorise. Your AI platform instantly syncs with your FYERS context—no command line, no code edits.

For everyday users, that means you’re ready in minutes.  
For developers, it means the installation doubles up as a foundation: the raw server spec is there if you want to extend MCP into your own custom setup.

Getting started with FYERS MCP is effortless


# For Mac & Windows

1. Download the installer for **Mac (.pkg)** or **Windows (.exe)**.  
2. Run the installer, it will install Node.js and Claude Desktop.  
3. Restart Claude Desktop and log in to your FYERS account.  
4. Authorise once, and your AI platform is synced with your FYERS context.  

👉 No coding. No command line. Just click, connect, and go.  

MacOS installer <a href="https://assets.fyers.in/mcp/macos/1.0.0/FYERS-Claude-MCP-Installer-v1.0.0.pkg">Click here</a>
[0m
[0m$ [0mcd /tmp/opencode; echo "=== v3_1 regulatory text ==="; python3 -c "
import yaml
d=yaml.safe_load(open('v3_1.yaml'))
print(d.get('info',{}).get('description',''))" | head -50
=== v3_1 regulatory text ===
As per the new retail algorithmic trading framework introduced by the Securities and Exchange Board of India (SEBI), the following changes will apply to order placement via the FYERS API.

**All API users must update their integration before March 31, 2026 to avoid disruption in order placement.
For more details refer to this [article](https://fyers.in/community/blogs-gdppin8d/post/sebi-s-new-algo-trading-rules-kick-in-on-april-1-here-is-what-changes-Yew3vdG4CgoXk1q).**
[0m
[0m$ [0mcd /tmp/opencode; grep -n "appIdHash" v3.yaml | head; echo "=== context lines ==="; grep -A3 -B3 "appIdHash" v3.yaml | head -40
842:         `appIdHash` | string | SHA-256 of api_id + app_secret <br/>Eg: SHA-256 of app_id:app_secret is <br/>c7120d2b5004f8de22d8dc2da0453b4d7e6211e37a4108b8371266ecff00498 <br/> You can use this <a href="https://emn178.github.io/online-tools/sha256.html" target="_blank">online tool</a> for reference
858:                     "grant_type":"authorization_code","appIdHash":"aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa","code":"eyJ..."
29615:        <li>POST the auth_code and appIdHash (SHA-256 of api_id + app_secret) to Validate Authcode API endpoint
29850:        `appIdHash` | string | SHA-256 of api_id + app_secret <br/>Eg: SHA-256 of app_id:app_secret is<br/>7c7120d2b5004f8de22d8dc2da0453b4d7e6211e37a4108b83712<br/>66ecff00498 <br/> You can use this <a href="https://emn178.github.io/online-tools/sha256.html" target="_blank">online tool</a> for reference
29868:                "appIdHash":"c3efb1075ef2332b3a4ec7d44b0f05c1********************",
30165:        `appIdHash` | string | SHA-256 of api_id + app_secret. Eg: SHA-256 of app_id:app_secret is c7120d2b5004f8de22d8dc2da0453b4d7e6211e37a4108b8371266ecff00498. You can use this online tool for reference (https://emn178.github.io/online-tools/sha256.html)
30176:              "appIdHash": "c3efb1075ef2332b3a4ec7d44b0f05c1********************",
=== context lines ===
         **Attribute** | **Data Type** | **Description**
         ---|---|---
         `grant_type` | string | This value must always be “authorization_code”
         `appIdHash` | string | SHA-256 of api_id + app_secret <br/>Eg: SHA-256 of app_id:app_secret is <br/>c7120d2b5004f8de22d8dc2da0453b4d7e6211e37a4108b8371266ecff00498 <br/> You can use this <a href="https://emn178.github.io/online-tools/sha256.html" target="_blank">online tool</a> for reference
         `code` | string | This is the auth_code which is received from the first step

         ## Response Attributes  
--
          source: |
            curl -H "Content-Type: application/json" -X POST -d 
               '{
                     "grant_type":"authorization_code","appIdHash":"aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa","code":"eyJ..."
               }' https://api-t1.fyers.in/api/v3/validate-authcode 

             ------------------------------------------------------------------------------------------------------------------------------------------
--
        <ol>
        <li>Navigate to the Login API endpoint</li>
        <li>After successful login, user is redirected to the redirect uri with the auth_code</li>
        <li>POST the auth_code and appIdHash (SHA-256 of api_id + app_secret) to Validate Authcode API endpoint
        </li>
        <li>Obtain the access_token use that for all the subsequent requests</li>
        </ol>
--
        **Attribute** | **Data Type** | **Description**
        ---|---|---
        `grant_type` | string | This value must always be “authorization_code
        `appIdHash` | string | SHA-256 of api_id + app_secret <br/>Eg: SHA-256 of app_id:app_secret is<br/>7c7120d2b5004f8de22d8dc2da0453b4d7e6211e37a4108b83712<br/>66ecff00498 <br/> You can use this <a href="https://emn178.github.io/online-tools/sha256.html" target="_blank">online tool</a> for reference
        `code` | string | This is the auth_code which is received from the first step

        ## Response Attributes 
--
            --header 'Content-Type: application/json' \
            --data-raw '{
                "grant_type":"authorization_code",  
                "appIdHash":"c3efb1075ef2332b3a4ec7d44b0f05c1********************",
                "code":"eyJ0eXAi*******.eyJpc3MiOiJhcGkubG9********.r_65Awa1kGdsNTAgD******"
            }'

--
[0m
[0m$ [0mcd /tmp/opencode; grep -n "generate-authcode" v3.yaml | head; echo "=== authcode flow ==="; grep -A15 "Navigate to the Login API endpoint" v3.yaml | head -25
29646:            curl --location --request GET 'https://api-t1.fyers.in/api/v3/generate-authcode?client_id=SPXXXXE7-100&redirect_uri=https://trade.fyers.in/api-login/redirect-uri/index.html&response_type=code&state=sample_state'
29680:            https://api-t1.fyers.in/api/v3/generate-authcode?
29709:            https://api-t1.fyers.in/api/v3/generate-authcode?
29739:            https://api-t1.fyers.in/api/v3/generate-authcode?
=== authcode flow ===
        <li>Navigate to the Login API endpoint</li>
        <li>After successful login, user is redirected to the redirect uri with the auth_code</li>
        <li>POST the auth_code and appIdHash (SHA-256 of api_id + app_secret) to Validate Authcode API endpoint
        </li>
        <li>Obtain the access_token use that for all the subsequent requests</li>
        </ol>
        <video class="app-flow-video" src="https://assets.fyers.in/myapi-dashboard/docs/authentication_user_flow.webm" autoplay loop muted playsinline />

    patch:
      tags:
        - Authentication & Login Flow - User Apps
      summary: Request Parameters for Step 1
      description: |
        ## Request Attributes 
        **Attribute** | **Data Type** | **Description**
        ---|---|---
[0m
[0m$ [0mecho "=== trade.fyers.in ==="; curl -s -m 15 -D - -o /dev/null "https://trade.fyers.in/api-login/redirect-uri/index.html" 2>&1 | grep -iE "^(HTTP|server|location|x-powered)"; echo "=== websocket hosts in spec ==="; grep -oE "wss://[a-z0-9.-]*fyers\.in[a-zA-Z0-9/_-]*" /tmp/opencode/v3.yaml | sort -u; grep -oE "(socket\.fyers|ws\.fyers)[a-z0-9/_.-]*" /tmp/opencode/v3.yaml | sort -u | head
=== trade.fyers.in ===
HTTP/2 200 
server: cloudflare
=== websocket hosts in spec ===
wss://rtsocket-api.fyers.in/versova
wss://socket.fyers.in/trade/v3
socket.fyers.in/trade/v3
[0m
[0m$ [0mfor h in trade.fyers.in socket.fyers.in rtsocket-api.fyers.in; do echo "=== $h ==="; dig +short $h A | head -2; curl -s -o /dev/null -w "  root -> %{http_code}\n" -m 12 "https://$h/"; done
=== trade.fyers.in ===
104.18.4.135
104.18.5.135
  root -> 200
=== socket.fyers.in ===
104.18.5.135
104.18.4.135
  root -> 404
=== rtsocket-api.fyers.in ===
104.18.5.135
104.18.4.135
  root -> 404
[0m
# Target: fyers.in
# Current Phase: SURFACE (IN_PROGRESS)

## Key Findings
- **Full v3 OpenAPI spec recovered** from `myapi.fyers.in/static/media/v3.fc0a0244d7d288c81e4f.yaml` (1.57 MB, 51 documented API paths) plus two supplemental specs: `v3.1.32a8eeba1fba866d1201.yaml` (SEBI retail-algo regulatory changes, effective Apr 2026) and `fia.9dcf545bc3f508df4db9.yaml` (new **FYERS MCP / Model Context Protocol** product).
- **Auth scheme confirmed live:** OAuth2 `authorization_code`; `appIdHash = SHA-256(app_id + ":" + app_secret)`; POST `/api/v3/validate-authcode {grant_type, appIdHash, code}`; access_token used as `Authorization: Bearer`. Live error fingerprinting: `-441` auth code required, `-442` invalid grant_type, `-16` invalid/expired token, `-501` invalid refresh token. `grant_type=refresh_token` accepted at `/api/v2/validate-refresh-token`; `authorization_code` rejected there (asymmetric v2/v3 handling).
- **New subdomains discovered** (from docs spec + probes): `trade.fyers.in` (200, OAuth redirect-uri host `/api-login/redirect-uri/index.html`), `socket.fyers.in` (`wss://socket.fyers.in/trade/v3`), `rtsocket-api.fyers.in` (`wss://rtsocket-api.fyers.in/versova`), `alerts.fyers.in`, `api-connect-docs.fyers.in` (branded order-placement docs + `fyers-lib.js`), `community.fyers.in`, `direct.fyers.in`, `learn.fyers.in`, `partners.fyers.in`.
- **New product surface:** FYERS MCP ships as a Mac `.pkg` / Windows `.exe` at `assets.fyers.in/mcp/macos/1.0.0/` that installs Node.js + Claude Desktop and performs its own FYERS OAuth login — client-side token storage / supply-chain surface (assets host is in scope).
- **marketdata-api-instaoptions.fyers.in** = Express service self-identifying as `{"service":"loom","version":"1.0.0"}` via `/health`; root 404s all paths probed.
- **api.fyers.in** (AWS ALB) exposed directly (no WAF), unlike api-t1/t2 (Cloudflare). Method enforcement confirmed: `OPTIONS /api/v3/order` -> 200; all others -> 500 with `{"message":"Invalid Request, please provide valid method"}`.
- API app management dashboard lives at `fyers.in/web/api-dashboard/user-apps` (Flutter web trading app on `fyers.in/web`). Docs portal SPA uses Redoc/Redocly and references `X-XSRF-TOKEN` CSRF handling.
- `workdrive.fyers.in`/`projects.fyers.in` redirect to Zoho SaaS (out-of-our-hands tenant; noted, deprioritized). `instantpayout.fyers.in` unreachable this run.

## API Endpoints (extracted from official spec, all `api-t1.fyers.in`)
- `POST /api/v3/generate-authcode?client_id&redirect_uri&response_type=code&state` (OAuth step 1)
- `POST /api/v3/validate-authcode` `{grant_type:"authorization_code", appIdHash, code}` (OAuth step 2)
- `POST /api/v3/validate-refresh-token` (refresh token grant)
- `POST /api/v3/logout`, `GET /api/v3/profile`, `GET /api/v3/funds`, `GET /api/v3/holdings`
- `GET/POST /api/v3/orders`, `GET /api/v3/orders/{id}/sync`, `POST /api/v3/orders/sync`, `POST /api/v3/orders/async`, `POST /api/v3/multi-order/sync`, `POST /api/v3/multi-order/async`, `POST /api/v3/multileg/orders/sync`, `GET/POST /api/v3/positions`
- `GET /api/v3/tradebook`, `GET /api/v3/order-history`, `GET /api/v3/trade-history`, `GET /api/v3/charges-history`, `GET /api/v3/realised-pnl-history`, `GET /api/v3/tax-pnl-history`, `GET /api/v3/ledger-history`
- `POST/PUT/PATCH/DELETE /api/v3/gtt/orders`, `POST /api/v3/gtt/orders/sync`
- `POST /api/v3/smart-order/{limit,trail,step,sip,modify,cancel,pause,resume,orderbook}`
- `GET /api/v3/price-alert`, `PUT /api/v3/toggle-alert`, `GET/POST /api/v3/multiorder/margin`
- `POST /api/v3/flows/tc/se`, `POST /api/v3/flows/tc/se/activate` (new SEBI TC/SE flow)
- `GET /data/history`, `GET /data/quotes?symbols`, `GET /data/depth`, `GET /data/options-chain-v3`, `GET /data/marketStatus`
- WebSockets: `wss://socket.fyers.in/trade/v3`, `wss://rtsocket-api.fyers.in/versova`
- Live error shapes: `GET /api/v3/positions` -> 401 (endpoint exists, auth-gated), `validate-authcode` -> 400 on empty POST.

## Hypotheses (pre-seeded for next phase)
### H1: OAuth `redirect_uri` validation bypass / auth-code leak
Reasoning: Step-1 `generate-authcode` takes user-supplied `client_id` + `redirect_uri`; the redirect-uri host is documented as `trade.fyers.in`, but no per-app allowlist evidence yet. If `redirect_uri` isn't strictly bound to the client_id, an attacker can register/abuse a client whose redirect_uri they control and receive the victim's `auth_code`; combined with the public `appIdHash` derivation scheme, code->token exchange is a single POST.
Impact: full trading-account session takeover (orders, funds, positions, PnL history). Read-only test design: fetch `generate-authcode` with a `redirect_uri` that is NOT the documented one and observe whether the redirect fires (open-redirect oracle); never exchange real codes.

### H2: `appIdHash` / refresh-token replay & rotation
Reasoning: `appIdHash` is a deterministic SHA-256(app_id:app_secret); refresh tokens validated via a grant that returned `-501` when missing. Weakness surface: refresh-token rotation (old token accepted after rotation?), token binding to app vs user, and whether `validate-refresh-token` leaks oracle info on partial inputs.

### H3: Authz gaps on new SEBI TC/SE flow (`/api/v3/flows/tc/se`)
Reasoning: Newly shipped compliance endpoints (`.../activate`) are the least-tested paths. Mis-scoped tenant checks on flows could expose enrollment status/state across accounts.

### H4: Reports/ledger parameter manipulation
Reasoning: `order-history`, `tax-pnl-history`, `realised-pnl-history` accept `fin_year`, `from_date`, `to_date`, `page_no`, `page_size`, `symbol` — potential for negative/oversized `page_size`, year-span abuse, or symbol cross-tenant leakage if filter applied post-authz.

### H5: Market-data API authz (instaoptions "loom")
Reasoning: Express service returns 404 on everything except `/health`; market depth / option-chain endpoints in the v3 docs may be reachable with weaker auth than the trading API.

### H6: MCP desktop installer OAuth/token handling
Reasoning: `.exe`/`.pkg` embeds its own login; token persistence, redirect-uri registration for the MCP client_id, and lack of pinning are client-side vectors tied to in-scope `assets.fyers.in` artifacts.

## CVSS Candidates
- [H1 OAuth redirect_uri / auth-code interception] - Estimated CVSS 8.1 (if code->token leakage confirmed)
- [H1 open redirect on generate-authcode] - Estimated CVSS 6.1 (browser-only oracle)
- [H2 refresh-token rotation flaw] - Estimated CVSS 7.5-9.0 (session persistence)
- [H3 TC/SE flow authz] - Estimated CVSS 6.5-8.0 (cross-tenant)
- [H4 report parameter abuse] - Estimated CVSS 5.3-6.5 (data exposure)

---
## STATE UPDATE FOR NEXT RUN
STATUS_TARGET: fyers.in
STATUS_PHASE: SURFACE
STATUS_STATE: IN_PROGRESS

NEXT_STEP_1: Verify `redirect_uri` binding by requesting `generate-authcode` with a non-documented redirect_uri and comparing redirect behavior vs documented `trade.fyers.in` URI (read-only oracle only).
NEXT_STEP_2: Enumerate v3 spec details for `flows/tc/se`, `validate-refresh-token`, and `smart-order` schemas in `v3.yaml` (params, required fields, examples) to tighten H2/H3/H4 designs.
NEXT_STEP_3: Probe `trade.fyers.in` (login + redirect-uri host) and `socket.fyers.in`/`rtsocket-api.fyers.in` handshake endpoints for auth gating and handshake response shapes; check `assets.fyers.in/mcp/` directory listing for artifact enumeration.
