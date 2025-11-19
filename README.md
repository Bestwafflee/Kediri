import random
import os
import time
import sys

_HAS_MS = False
try:
    import msvcrt
    _HAS_MS = True
except Exception:
    _HAS_MS = False

FAST_LOG = True
LOG_DELAY = 0.01 if FAST_LOG else 0.04
QTE_TIMEOUT = 1.5
EVOLUTION_QTE_STEPS = 3

skip_all_dialog = False

def slow_print(text, delay=LOG_DELAY):
    """Print text slowly. On Windows we support pressing SPACE to speed,
    pressing 'q' toggles skip. On other platforms, we simply print text
    (safe fallback)."""
    global skip_all_dialog
    if skip_all_dialog or delay <= 0 or not _HAS_MS:
        print(text)
        return

    fast_mode = False
    for c in text:
        if _HAS_MS and msvcrt.kbhit():
            key = msvcrt.getch()
            if key == b' ':
                fast_mode = True
            elif key.lower() == b'q':
                skip_all_dialog = not skip_all_dialog
                status = "ON" if skip_all_dialog else "OFF"
                print(f"\n[Skip Dialog: {status}]")
                time.sleep(0.15)
                if skip_all_dialog:
                    print(text)
                    return

        current_delay = delay * 0.1 if fast_mode else delay
        print(c, end='', flush=True)
        time.sleep(current_delay)

    print()
    if fast_mode:
        time.sleep(0.02)

def pause(prompt="\nTekan [Enter] untuk lanjut (atau ketik 'q' lalu Enter untuk toggle skip): "):
    """Pause — safe cross-platform. If user types 'q' and Enter, toggle skip_all_dialog."""
    global skip_all_dialog
    if skip_all_dialog:
        return
    try:
        resp = input(prompt).strip().lower()
        if resp == 'q':
            skip_all_dialog = not skip_all_dialog
            status = "ON" if skip_all_dialog else "OFF"
            print(f"[Skip Dialog: {status}]")
            time.sleep(0.15)
    except (KeyboardInterrupt, EOFError):
        pass

def clear():
    os.system('cls' if os.name == 'nt' else 'clear')

GAME_TITLE = "Kediri"
INTRO = [
    "Tahun 1120 M — Kediri, sebuah kerajaan megah di tanah Jawa yang menjadi pusat ilmu, sastra, dan perdagangan di masa kejayaannya.",
    "Kediri dipimpin oleh Raja Jayabaya, sosok yang dikenal bijaksana dan memiliki penglihatan jauh ke masa depan.",
    "Namun, perebutan tahta dan nafsu kekuasaan menimbulkan perang saudara antara Panjalu dan Jenggala.",
    "Di tengah kekacauan itu, kabar tentang artefact kuno tersebar — konon dapat mengembalikan keseimbangan Kediri.",
    "Kamu adalah seorang Penjaga Sejarah — murid brahmana istana, diberi misi: temukan artefact dan pulihkan Kediri!"
]

quiz_kediri = [
    ("Apakah Kediri merupakan salah satu kerajaan tertua di Jawa Timur? (Y/T)", "Y"),
    ("Apakah Raja Jayabaya dikenal sebagai raja bijaksana dari Kediri? (Y/T)", "Y"),
    ("Apakah Kediri pernah menjadi bagian dari Kerajaan Majapahit? (Y/T)", "T"),
    ("Apakah Kerajaan Kediri berdiri pada abad ke-18? (Y/T)", "T"),
    ("Apakah Panjalu adalah nama lain dari Kerajaan Kediri? (Y/T)", "Y"),
    ("Apakah Jenggala dan Kediri dulunya satu kerajaan sebelum terpecah? (Y/T)", "Y"),
    ("Apakah Prabu Jayabaya dikenal dengan ramalannya yang disebut 'Jangka Jayabaya'? (Y/T)", "Y"),
    ("Apakah Sungai Brantas memiliki peran penting dalam sejarah Kediri? (Y/T)", "Y"),
    ("Apakah Kediri pernah menjadi pusat perdagangan rempah di masa lalu? (Y/T)", "Y"),
    ("Apakah ibu kota Kediri kuno terletak di wilayah barat Pulau Sumatra? (Y/T)", "T"),
    ("Apakah prasasti Kwak dan prasasti Banjaran ditemukan di wilayah Kediri? (Y/T)", "Y"),
    ("Apakah agama yang berkembang di masa Kediri adalah Hindu dan Buddha? (Y/T)", "Y"),
    ("Apakah Kerajaan Kediri dikenal sebagai masa keemasan sastra Jawa Kuno? (Y/T)", "Y"),
    ("Apakah kitab Bharatayudha ditulis pada masa Kerajaan Kediri? (Y/T)", "Y"),
    ("Apakah Kediri modern sekarang termasuk wilayah Provinsi Jawa Tengah? (Y/T)", "T"),
    ("Apakah lambang Kota Kediri sekarang menampilkan gambar Gunung Kelud? (Y/T)", "Y"),
    ("Apakah Gunung Kelud terletak tidak jauh dari wilayah Kediri? (Y/T)", "Y"),
    ("Apakah Prabu Kertajaya adalah raja terakhir Kediri sebelum Majapahit? (Y/T)", "Y"),
    ("Apakah Kediri terkenal sebagai kota tahu? (Y/T)", "Y"),
    ("Apakah Sungai Brantas mengalir melewati pusat Kota Kediri? (Y/T)", "Y"),
    ("Apakah Kerajaan Kediri mencapai puncak kejayaannya pada masa Jayabaya? (Y/T)", "Y"),
    ("Apakah Kerajaan Kediri dan Singhasari pernah berperang? (Y/T)", "Y"),
    ("Apakah Kertajaya dikalahkan oleh Ken Arok dalam pertempuran Ganter? (Y/T)", "Y"),
    ("Apakah Prasasti Panumbangan berasal dari masa Kediri? (Y/T)", "Y"),
    ("Apakah Kerajaan Kediri dikenal juga dengan sebutan Daha? (Y/T)", "Y"),
    ("Apakah ekonomi Kediri kuno sangat bergantung pada jalur Sungai Brantas? (Y/T)", "Y"),
    ("Apakah kitab Smaradhana ditulis pada zaman Kediri? (Y/T)", "Y"),
    ("Apakah Kediri pernah diperintah oleh raja wanita? (Y/T)", "T"),
    ("Apakah seni sastra berkembang pesat pada masa Raja Kameçvara? (Y/T)", "Y"),
    ("Apakah bendera Kerajaan Kediri masih dipakai sebagai simbol resmi modern? (Y/T)", "T"),
    ("Apakah Kediri pernah berselisih dengan Jenggala setelah perpecahan Airlangga? (Y/T)", "Y"),
    ("Apakah Airlangga adalah pendiri Kerajaan Kediri secara langsung? (Y/T)", "T"),
    ("Apakah Kediri pernah dikenal sebagai pusat budaya Hindu-Buddha di Jawa Timur? (Y/T)", "Y"),
    ("Apakah cerita Panji berkembang pada masa Kediri? (Y/T)", "Y"),
    ("Apakah Kediri runtuh akibat serangan Mongol? (Y/T)", "T"),
    ("Apakah pasar-pasar kuno di Kediri terhubung melalui jalur sungai? (Y/T)", "Y"),
    ("Apakah Kerajaan Kediri secara geografis berada di daerah subur? (Y/T)", "Y"),
    ("Apakah Kertajaya dianggap melawan para brahmana sehingga kehilangan dukungan? (Y/T)", "Y"),
    ("Apakah nama Daha kemudian dipakai kembali pada masa Majapahit? (Y/T)", "Y"),
    ("Apakah Kediri pernah menjadi pusat kekuatan politik terbesar di Jawa? (Y/T)", "Y"),
    ("Apakah semua raja Kediri merupakan keturunan langsung Airlangga? (Y/T)", "T"),
    ("Apakah kerajaan Kediri dikenal sebagai kerajaan maritim besar? (Y/T)", "T"),
    ("Apakah Kediri memiliki hubungan diplomatik dengan kerajaan-kerajaan lain di Nusantara? (Y/T)", "Y"),
    ("Apakah kitab Lubdhaka ditulis pada masa Kediri? (Y/T)", "Y"),
    ("Apakah Jayabaya dipercaya memiliki kemampuan meramal masa depan? (Y/T)", "Y"),
    ("Apakah masyarakat Kediri kuno mayoritas berprofesi sebagai petani dan pedagang? (Y/T)", "Y"),
    ("Apakah Gunung Klotok merupakan salah satu situs bersejarah di Kediri? (Y/T)", "Y"),
    ("Apakah Kediri dikenal sebagai pusat industri gula pada masa kolonial? (Y/T)", "Y"),
    ("Apakah Kerajaan Kediri masih bertahan hingga abad ke-16? (Y/T)", "T"),
    ("Apakah nama Kediri berasal dari kata Kadiri yang berarti pohon cemara? (Y/T)", "T"),
    ("Apakah wilayah Kediri saat ini terbagi menjadi kota dan kabupaten? (Y/T)", "Y"),
    ("Apakah Sungai Brantas dulu digunakan sebagai jalur transportasi utama di Kediri? (Y/T)", "Y"),
    ("Apakah Candi Tondowongso ditemukan di wilayah Kediri modern? (Y/T)", "Y"),
    ("Apakah Kerajaan Kediri pernah memindahkan pusat kekuasaannya ke luar Jawa? (Y/T)", "T"),
    ("Apakah peninggalan sejarah Kediri banyak ditemukan dalam bentuk candi dan prasasti? (Y/T)", "Y"),
]

LOCATIONS = {
    "Gunung Klotok": {
        "desc": "Pegunungan batu, rute sulit tapi dekat dengan pusat Daha.",
        "artefact": True,
        "artefact_obtained": False,
        "enemies": [("Buto Ijo", 200, 11, 10, 10), ("Perampok", 100, 20, 90, 20)],
        "npc": "Seorang gadis — meminta bantuan untuk memperbaiki jalan dan menggendongnya pulang."
    },
    "Setono Gedong": {
        "desc": "Situs lama yang hampir hancur.",
        "artefact": True,
        "artefact_obtained": False,
        "enemies": [("Genderuwo", 350, 50, 50, 50), ("Siluman", 120, 21, 50, 22)],
        "npc": "Seorang pria yang kesakitan di pinggir jalan."
    },
    "Hutan Joyoboyo": {
        "desc": "Hutan lebat yang penuh cerita dan bahaya.",
        "artefact": True,
        "artefact_obtained": False,
        "enemies": [("Hantu", 100, 10, 25, 25), ("Rangda", 200, 10, 10, 12)],
        "npc": "Ada sebuah mayat yang tampak bergerak."
    },
    "Reruntuhan Daha": {
        "desc": "Sisa-sisa kota tua Daha; naskah usang dan roh penjaga arsip berkeliaran.",
        "artefact": False,
        "artefact_obtained": False,
        "enemies": [("Prasasti", 120, 15, 52, 11), ("Arwah Juru Tulis", 145, 12, 55, 29)],
        "npc": "Pustaka Keraton yang mencari lembar hilang dari Kitab Panji."
    },
    "Simpang lima gumul": {
        "desc": "Pusat kekuatan spiritual Daha. Masuk setelah seluruh artefact dipulihkan.",
        "artefact": False,
        "artefact_obtained": False,
        "enemies": [("Penjaga Inti", 750, 50, 50, 150)],
        "npc": "Roh Mahapatih — ujian terakhir bagi sang pengembara."
    }
}

TOTAL_ARTEFACT = sum(1 for v in LOCATIONS.values() if v.get("artefact"))

def qte_step(expected_char, timeout=QTE_TIMEOUT):
    """Wait for single key press matching expected_char within timeout (case-insensitive).
    On non-Windows, fallback to input() prompt."""
    start = time.monotonic()
    if _HAS_MS:
        while time.monotonic() - start < timeout:
            if msvcrt.kbhit():
                key = msvcrt.getch()
                try:
                    ch = key.decode('utf-8').lower()
                except Exception:
                    ch = ''
                if ch == expected_char.lower():
                    return True
                else:
                    return False
            time.sleep(0.01)
        return False
    else:
        try:
            resp = input(f"Tekan '{expected_char}' lalu Enter (timeout {timeout:.1f}s): ").strip().lower()
            return resp == expected_char.lower()
        except Exception:
            return False

def qte_sequence(steps=3, timeout=QTE_TIMEOUT):
    """Create a simple random QTE sequence, return True if all correct."""
    letters = [random.choice('asdfjkl;nmqwerty12345') for _ in range(steps)]
    slow_print("QTE (quick time event): Tekan urutan tombol berikut, satu per satu.")
    slow_print("Urutan: " + ' '.join(letters))
    time.sleep(0.15)
    for ch in letters:
        slow_print(f"Tekan '{ch}' sekarang! (timeout {timeout:.1f}s)")
        ok = qte_step(ch, timeout=timeout)
        if not ok:
            slow_print("QTE gagal.")
            return False
        else:
            slow_print("Berhasil.")
    slow_print("QTE selesai dengan sukses.")
    return True

class Player:
    def __init__(self, name, job):
        self.name = name
        self.job = job
        self.level = 1
        self.exp = 0
        self.gold = 100
        self.lifesteal = 0.0
        self.max_prana = 0
        self.prana = 0
        self.arrows = 0
        self.forged_sentinels = 0
        self.forged_sentinels_active_turns = 0
        self.forged_sentinels_used_this_battle = False

        if job == "Kesatria panji":
            self.max_hp = 200
            self.attack = 25
            self.lifesteal = 0.25
        elif job == "Brahmana janggala":
            self.max_hp = 125
            self.attack = 40
            self.max_prana = 100
            self.prana = 100
        elif job == "Pemanah daha":
            self.max_hp = 100
            self.attack = 30
            self.arrows = 10
        elif job == "Pandhita wesi":
            self.max_hp = 100
            self.attack = 25
            self.forged_sentinels = 1
        else:
            self.max_hp = 100
            self.attack = 10

        self.hp = self.max_hp
        self.potions = 1
        self.keranjang = 1
        self.status = {"burn": {"turns": 0, "source_atk": 0}, "bleed": {"turns": 0}}
        self.is_Jayabaya = False
        self.Jayabaya_shield_turns = 0
        self.is_dev = False
        self.dev_godmode = False
        self.dev_instakill = False
        self.dev_unlimited_gold = False
        self.dev_unlimited_exp = False
        self.dev_max_stats = False
        self.dev_free_skills = False
        self.dev_full_heal = False
        self.dev_get_all_artefacts = False

    def add_exp(self, amount):
        self.exp += amount
        while self.exp >= self.level * 60:
            self.exp -= self.level * 60
            self.level_up()

    def level_up(self):
        self.level += 1
        self.max_hp += 25
        self.attack += 5
        self.hp = self.max_hp
        if self.job == "Brahmana janggala":
            self.max_prana += 10
            self.prana = self.max_prana
        elif self.job == "Pemanah daha":
            self.arrows += 3
        slow_print(f"\n{self.name} naik ke level {self.level}!")

    def heal(self, amount):
        self.hp = min(self.max_hp, self.hp + amount)

    def spend_prana(self, amount):
        if self.job == "Brahmana janggala" and self.prana >= amount:
            self.prana -= amount
            return True
        return False

    def spend_forged_sentinels_item(self, amount):
        if self.job == "Pandhita wesi" and self.forged_sentinels >= amount:
            self.forged_sentinels -= amount
            return True
        return False

    def reset_battle_state(self):
        self.status = {"burn": {"turns": 0, "source_atk": 0}, "bleed": {"turns": 0}}
        self.forged_sentinels_active_turns = 0
        self.forged_sentinels_used_this_battle = False
        self.Jayabaya_shield_turns = 0

    def evolve_to_Jayabaya(self):
        self.job = "Raja Jayabaya"
        self.is_Jayabaya = True
        self.max_hp = max(self.max_hp, 500)
        self.hp = self.max_hp
        self.attack = max(self.attack, 60)
        self.lifesteal = 0.5
        slow_print("Evolusi sukses. Kamu kini menjadi Raja Jayabaya — sangat sulit untuk dimatikan.")

class Enemy:
    def __init__(self, name, hp, attack, gold, exp, can_burn=False):
        self.name = name
        self.hp = hp
        self.attack = attack
        self.gold = gold
        self.exp = exp
        self.status = {"burn": {"turns": 0, "source_atk": 0}, "bleed": {"turns": 0}}
        self.can_burn = can_burn

    def reset_battle_state(self):
        self.status = {"burn": {"turns": 0, "source_atk": 0}, "bleed": {"turns": 0}}

def apply_burn_to_enemy(enemy, source_atk, turns=3):
    if enemy.status["burn"]["turns"] <= 0:
        enemy.status["burn"] = {"turns": turns, "source_atk": source_atk}
        slow_print(f"{enemy.name} terbakar! ({turns} turn)")
    else:
        enemy.status["burn"]["turns"] = max(enemy.status["burn"]["turns"], turns)
        slow_print(f"{enemy.name} terbakar lagi! (durasi disegarkan)")

def apply_bleed_to_enemy(enemy, turns=3):
    if enemy.status["bleed"]["turns"] <= 0:
        enemy.status["bleed"] = {"turns": turns}
        slow_print(f"{enemy.name} berdarah! ({turns} turn)")
    else:
        enemy.status["bleed"]["turns"] = max(enemy.status["bleed"]["turns"], turns)
        slow_print(f"{enemy.name} semakin berdarah! (durasi disegarkan)")

def apply_burn_to_player(player, source_atk, turns=3):
    if player.status["burn"]["turns"] <= 0:
        player.status["burn"] = {"turns": turns, "source_atk": source_atk}
        slow_print(f"{player.name} terbakar! ({turns} turn)")
    else:
        player.status["burn"]["turns"] = max(player.status["burn"]["turns"], turns)
        slow_print(f"{player.name} terbakar lagi! (durasi disegarkan)")

def apply_bleed_to_player(player, turns=3):
    if player.status["bleed"]["turns"] <= 0:
        player.status["bleed"] = {"turns": turns}
        slow_print(f"{player.name} berdarah! ({turns} turn)")
    else:
        player.status["bleed"]["turns"] = max(player.status["bleed"]["turns"], turns)
        slow_print(f"{player.name} semakin berdarah! (durasi disegarkan)")

def quiz_attack(player, enemy):
    max_attempts = 5
    for attempt in range(1, max_attempts + 1):
        q, ans = random.choice(quiz_kediri)
        print(f"\n[ Percobaan {attempt}/{max_attempts} ]")
        print(f"Pertanyaan: {q}")
        jawab = input("Jawabanmu: ").strip().lower()
        if jawab == ans.lower():
            dmg = random.randint(max(1, player.attack - 3), player.attack + 5)
            enemy.hp -= dmg
            slow_print(f"Benar! Serangan berhasil dengan {dmg} damage!\n")
            if player.job == "Brahmana janggala" and random.random() < 0.4:
                apply_burn_to_enemy(enemy, player.attack, turns=3)
            if player.job == "Kesatria panji" and random.random() < 0.3:
                extra_dmg = random.randint(3, 10)
                enemy.hp -= extra_dmg
                slow_print(f"Serangan kedua! Kamu memberikan {extra_dmg} damage tambahan!\n")
                dmg += extra_dmg
            if random.random() < 0.35:
                slow_print(f"{enemy.name} terguncang dan mungkin gagal menyerang balik!")
                player_hit = True
            else:
                player_hit = True
            return dmg
        else:
            slow_print("Jawaban salah!\n")
    heavy_damage = random.randint(15, 30)
    player.hp -= heavy_damage
    slow_print(
        f"\nKamu gagal menjawab semua pertanyaan!"
        f"\nMonster memukulmu keras dan memberikan {heavy_damage} damage!"
        "\nKamu kehilangan kesempatan menyerang!"
    )
    return 0

def quiz_revive(player):
    clear()
    q, ans = random.choice(quiz_kediri)
    slow_print("\nKesempatan Kedua! Jawab pertanyaan sejarah Kediri untuk bangkit kembali.")
    print(q)
    jawaban = input("Jawabanmu: ").strip().lower()
    if jawaban == ans.lower():
        slow_print("\nJawaban benar! Semangat juangmu memulihkan 50% HP maksimum.")
        player.hp = max(1, player.max_hp // 2)
        pause()
        return True
    else:
        slow_print("\nJawaban salah... Perjuanganmu berakhir.")
        pause()
        return False

def process_status_effects_start_of_turn(player, enemy):
    if player.status["burn"]["turns"] > 0:
        dmg = max(1, int(player.status["burn"]["source_atk"] * 0.10))
        player.hp -= dmg
        slow_print(f"{player.name} terbakar! (-{dmg} HP)")
        player.status["burn"]["turns"] -= 1
    if player.status["bleed"]["turns"] > 0:
        dmg = max(1, int(player.max_hp * 0.03))
        player.hp -= dmg
        slow_print(f"{player.name} berdarah! (-{dmg} HP)")
        player.status["bleed"]["turns"] -= 1

    if enemy.status["burn"]["turns"] > 0:
        dmg = max(1, int(enemy.status["burn"]["source_atk"] * 0.10))
        enemy.hp -= dmg
        slow_print(f"{enemy.name} terbakar! (-{dmg} HP)")
        enemy.status["burn"]["turns"] -= 1
    if enemy.status["bleed"]["turns"] > 0:
        dmg = max(1, int(enemy.hp * 0.05))
        enemy.hp -= dmg
        slow_print(f"{enemy.name} berdarah! (-{dmg} HP)")
        enemy.status["bleed"]["turns"] -= 1

def process_forged_sentinels_attack(player, enemy):
    if player.forged_sentinels_active_turns > 0:
        dmg = max(1, int(player.attack * 0.25))
        enemy.hp -= dmg
        slow_print(f"Forged Sentinels menembak! (-{dmg} HP)")
        player.forged_sentinels_active_turns -= 1
        if player.forged_sentinels_active_turns == 0:
            player.forged_sentinels_used_this_battle = False
            slow_print("Forged Sentinels telah berhenti beroperasi.")

def qte_enhance_skill(skill_name):
    slow_print(f"QTE untuk meningkatkan {skill_name}. Siap?")
    ok = qte_sequence(steps=2, timeout=QTE_TIMEOUT)
    return ok

def dev_mode(player):
    """Developer Mode menu.
    Activated when player name is 'DEV' (case-insensitive). Cheats toggled here.
    """
    slow_print("\n=== DEVELOPER MODE ===", 0.001)
    player.is_dev = True
    # ensure flags exist
    for flag in ["dev_godmode", "dev_instakill", "dev_unlimited_gold", "dev_unlimited_exp",
                 "dev_max_stats", "dev_free_skills", "dev_full_heal", "dev_get_all_artefacts"]:
        if not hasattr(player, flag):
            setattr(player, flag, False)
    while True:
        slow_print("\nDeveloper Menu: pilih aksi:")
        print("[1] Toggle GODMODE (kebal)          - currently:", player.dev_godmode)
        print("[2] Toggle INSTA-KILL (next battles)- currently:", player.dev_instakill)
        print("[3] Toggle UNLIMITED GOLD           - currently:", player.dev_unlimited_gold)
        print("[4] Toggle UNLIMITED EXP            - currently:", player.dev_unlimited_exp)
        print("[5] Toggle MAX STATS (set max values)- currently:", player.dev_max_stats)
        print("[6] Toggle FREE SKILLS (no resource)- currently:", player.dev_free_skills)
        print("[7] FULL HEAL now                    - one-time effect")
        print("[8] GET ALL ARTEFACTS (one-time)     - award all artefacts")
        print("[9] INSTANT WIN (ending)             - immediate victory ending")
        print("[0] INSTANT LOSE (ending)            - immediate death ending")
        print("[Q] Kembali ke permainan")

        cmd = input("> ").strip().upper()
        if cmd == "1":
            player.dev_godmode = not player.dev_godmode
            slow_print("GODMODE set to: " + str(player.dev_godmode))
            if player.dev_godmode:
                player.hp = player.max_hp
        elif cmd == "2":
            player.dev_instakill = not player.dev_instakill
            slow_print("INSTA-KILL set to: " + str(player.dev_instakill))
        elif cmd == "3":
            player.dev_unlimited_gold = not player.dev_unlimited_gold
            slow_print("UNLIMITED GOLD set to: " + str(player.dev_unlimited_gold))
            if player.dev_unlimited_gold:
                player.gold = 10**9
        elif cmd == "4":
            player.dev_unlimited_exp = not player.dev_unlimited_exp
            slow_print("UNLIMITED EXP set to: " + str(player.dev_unlimited_exp))
            if player.dev_unlimited_exp:
                player.level = max(player.level, 50)
                player.exp = 0
        elif cmd == "5":
            player.dev_max_stats = not player.dev_max_stats
            slow_print("MAX STATS set to: " + str(player.dev_max_stats))
            if player.dev_max_stats:
                player.attack = max(player.attack, 999)
                player.max_hp = max(player.max_hp, 9999)
                player.hp = player.max_hp
        elif cmd == "6":
            player.dev_free_skills = not player.dev_free_skills
            slow_print("FREE SKILLS set to: " + str(player.dev_free_skills))
            if player.dev_free_skills:
                player.prana = getattr(player, "max_prana", player.prana) or 9999
                player.arrows = getattr(player, "arrows", 9999) or 9999
                player.forged_sentinels = getattr(player, "forged_sentinels", 9999) or 9999
        elif cmd == "7":
            player.hp = player.max_hp
            if getattr(player, "max_prana", 0):
                player.prana = player.max_prana
            slow_print("Kamu disembuhkan penuh!")
        elif cmd == "8":
            if 'LOCATIONS' in globals():
                for k, v in LOCATIONS.items():
                    if v.get("artefact"):
                        v["artefact_obtained"] = True
                slow_print("Semua artefact telah diberikan.")
            else:
                slow_print("Tidak dapat menemukan lokasi artefact (LOCATIONS tidak tersedia).")
        elif cmd == "9":
            slow_print("Cheat: INSTANT WIN")
            show_ending_victory(player)
            if getattr(player, "is_Jayabaya", False):
                show_ending_Jayabaya(player)
            sys.exit(0)
        elif cmd == "0":
            slow_print("Cheat: INSTANT LOSE")
            show_ending_death()
            sys.exit(0)
        elif cmd == "Q":
            slow_print("Keluar dari Developer Menu.")
            break
        else:
            slow_print("Perintah tidak dikenal.")

def battle(player, enemy):
    player.reset_battle_state()
    enemy.reset_battle_state()
    if getattr(player, "is_dev", False):
        if getattr(player, "dev_godmode", False):
            player.hp = player.max_hp
        if getattr(player, "dev_instakill", False):
            enemy.hp = 0

    if enemy.name in ("Penjaga Inti", "Rangda", "Hantu"):
        enemy.can_burn = True

    clear()
    slow_print(f"Pertempuran: {player.name} vs {enemy.name}\n")

    while player.hp > 0 and enemy.hp > 0:
        if getattr(player, "is_dev", False) and getattr(player, "dev_godmode", False):
            player.hp = player.max_hp
        if getattr(player, "is_dev", False) and getattr(player, "dev_free_skills", False):
            player.prana = getattr(player, "max_prana", player.prana)
            player.arrows = max(player.arrows, 9999)
            player.forged_sentinels = max(getattr(player, "forged_sentinels", 0), 9999)

        process_status_effects_start_of_turn(player, enemy)
        if player.hp <= 0 or enemy.hp <= 0:
            break

        process_forged_sentinels_attack(player, enemy)
        if enemy.hp <= 0:
            break

        print(f"{player.name}: {player.hp}/{player.max_hp} HP", end="  ")
        if player.job == "Brahmana janggala":
            print(f"| Prana: {player.prana}/{player.max_prana}", end="  ")
        elif player.job == "Pemanah daha":
            print(f"| Arrows: {player.arrows}", end="  ")
        elif player.job == "Pandhita wesi":
            print(f"| Forged Sentinels inv: {player.forged_sentinels}", end="  ")
            if player.forged_sentinels_active_turns > 0:
                print(f"(Active: {player.forged_sentinels_active_turns} turn)", end="  ")
        if player.is_Jayabaya:
            print(f"| Shield: {player.Jayabaya_shield_turns} turn", end="  ")
        print(f"| {player.gold} Gold | Potions: {player.potions} | Keranjang: {player.keranjang}")
        print(f"{enemy.name}: {enemy.hp} HP\n")

        options = ["[1] Serang (Jawab pertanyaan untuk menyerang)"]
        if player.job == "Kesatria panji":
            options.append("[2] Bloodlust (Korbankan 20 HP, serangan kuat)")
        elif player.job == "Brahmana janggala":
            options.append("[2] Fireball (25 Prana) | [3] Heal (20 Prana)")
        elif player.job == "Pemanah daha":
            options.append("[2] Arrow Rain (3 Arrow multi-hit)")
        elif player.job == "Pandhita wesi":
            options.append("[2] Deploy Forged Sentinels (1 Forged Sentinel)")
        if player.is_Jayabaya:
            options.append("[2] Overload (QTE powerful)")
        options.append("[P] Potion | [R] Kabur")
        for o in options:
            print(o)
        choice = input("> ").strip().upper()
        player_hit = False
        dmg = 0

        if player.job == "Kesatria panji" and choice == "2":
            if player.hp > 20:
                player.hp -= 20
                dmg = random.randint(player.attack, player.attack + 10)
                inflicted = int(dmg * 1.5)
                slow_print("Bloodlust: lakukan QTE untuk meningkatkan damage.")
                if qte_enhance_skill("Bloodlust"):
                    inflicted *= 2
                    slow_print("QTE sukses! Damage Bloodlust meningkat.")
                enemy.hp -= inflicted
                slow_print(f"Bloodlust aktif! Kamu mengorbankan darah untuk {inflicted} damage!")
                player_hit = True
                if random.random() < 0.3:
                    apply_bleed_to_enemy(enemy, turns=3)
            else:
                slow_print("HP terlalu rendah untuk Bloodlust!")

        elif player.job == "Brahmana janggala" and choice == "2":
            if player.spend_prana(25):
                dmg = random.randint(player.attack + 10, player.attack + 25)
                slow_print("Fireball: lakukan QTE untuk memperkuat dan menjamin burn.")
                if qte_enhance_skill("Fireball"):
                    dmg += int(player.attack * 0.5)
                    apply_burn_to_enemy(enemy, player.attack, turns=3)
                    slow_print("QTE sukses! Fireball lebih kuat dan menyebabkan burn.")
                else:
                    if random.random() < 0.4:
                        apply_burn_to_enemy(enemy, player.attack, turns=3)
                enemy.hp -= dmg
                slow_print(f"Fireball meledak! Musuh terkena {dmg} damage!")
                player_hit = True
            else:
                slow_print("Prana tidak cukup!")

        elif player.job == "Brahmana janggala" and choice == "3":
            if player.spend_prana(20):
                player.heal(30)
                slow_print("Kamu menyembuhkan diri +30 HP.")
            else:
                slow_print("Prana tidak cukup!")

        elif player.job == "Pemanah daha" and choice == "2":
            if player.arrows >= 3:
                player.arrows -= 3
                hits = random.randint(2, 4)
                slow_print("Arrow Rain: QTE untuk menambah jumlah panah/kerusakan.")
                if qte_enhance_skill("Arrow Rain"):
                    extra = 2
                    hits += extra
                    slow_print(f"QTE sukses! Jumlah hits bertambah +{extra}.")
                total = 0
                for _ in range(hits):
                    hit = random.randint(max(1, player.attack - 3), player.attack + 4)
                    enemy.hp -= hit
                    total += hit
                slow_print(f"Arrow Rain menghujani musuh ({hits} panah) total {total} damage!")
                player_hit = True
                if random.random() < 0.5:
                    apply_bleed_to_enemy(enemy, turns=2)
            else:
                slow_print("Panah tidak cukup!")

        elif player.job == "Pandhita wesi" and choice == "2":
            if player.forged_sentinels_active_turns > 0:
                slow_print("Forged Sentinels sudah aktif! Tunggu sampai berhenti sebelum memasang lagi.")
            elif player.forged_sentinels <= 0:
                slow_print("Tidak ada Forged Sentinels di inventori!")
            else:
                slow_print("Deploy Forged Sentinels: QTE untuk memperpanjang durasi/damage Forged Sentinels.")
                ok = qte_enhance_skill("Deploy Forged Sentinels")
                player.spend_forged_sentinels_item(1)
                player.forged_sentinels_active_turns = 4 if ok else 3
                player.forged_sentinels_used_this_battle = True
                if ok:
                    slow_print("QTE sukses! Forged Sentinels aktif lebih lama dan lebih kuat.")
                slow_print(f"Kamu memasang Forged Sentinels! Ia akan menembak selama {player.forged_sentinels_active_turns} turn.")

        elif player.is_Jayabaya and choice == "2":
            slow_print("Overload: lakukan QTE untuk memaksimalkan serangan.")
            ok = qte_sequence(steps=3, timeout=QTE_TIMEOUT)
            if ok:
                dmg = int(player.attack * 4)
                enemy.hp -= dmg
                player.Jayabaya_shield_turns = 2
                slow_print(f"Overload sukses! Musuh terkena {dmg} damage dan kamu mendapat shield 2 turn.")
            else:
                dmg = int(player.attack * 1.5)
                enemy.hp -= dmg
                slow_print(f"Overload gagal sebagian. Musuh terkena {dmg} damage.")
            player_hit = True

        elif choice == "1":
            dmg = quiz_attack(player, enemy)
            if dmg > 0:
                player_hit = True
                if player.job == "Kesatria panji":
                    heal_amt = int(dmg * player.lifesteal)
                    player.heal(heal_amt)
                    slow_print(f"Lifesteal memulihkan {heal_amt} HP!")

        elif choice == "P":
            if player.potions > 0:
                player.potions -= 1
                player.heal(30)
                slow_print("Kamu memulihkan 30 HP!")
            else:
                slow_print("Tidak ada potion!")

        elif choice == "R":
            slow_print("Kamu kabur dari pertarungan!")
            time.sleep(0.2)
            return False

        else:
            slow_print("Pilihan tidak valid.")
            time.sleep(0.2)
            continue

        if enemy.hp > 0:
            if player_hit and random.random() < 0.35:
                slow_print(f"{enemy.name} goyah dan gagal menyerang balik!")
            else:
                dmg = random.randint(max(1, enemy.attack - 3), enemy.attack + 3)
                if player.Jayabaya_shield_turns > 0:
                    dmg = int(dmg * 0.4)
                player.hp -= dmg
                slow_print(f"{enemy.name} menyerangmu sebesar {dmg} damage!")
            if enemy.can_burn and enemy.hp > 0 and random.random() < 0.30:
                apply_burn_to_player(player, enemy.attack, turns=3)

        if player.job == "Brahmana janggala":
            player.prana = min(player.max_prana, player.prana + 10)
        elif player.job == "Pemanah daha":
            if random.random() < 0.3:
                player.arrows += 1
                slow_print("Kamu menemukan 1 anak panah di tanah.")
        if player.Jayabaya_shield_turns > 0:
            player.Jayabaya_shield_turns -= 1

        time.sleep(0.05)
        clear()

    if player.hp <= 0:
        slow_print("Kamu tumbang...")
        survived = quiz_revive(player)
        return not survived
    else:
        slow_print(f"Kamu mengalahkan {enemy.name}!")
        player.gold += enemy.gold
        player.add_exp(enemy.exp)
        if random.random() < 0.35:
            player.keranjang += 1
            slow_print("Musuh menjatuhkan 1 Keranjang!")
        slow_print(f"+{enemy.exp} EXP, +{enemy.gold} Gold")
        pause()
        return False

def shop(player):
    clear()
    slow_print("Toko — Barang tersedia:")
    print("[1] Potion (+30 HP) - 15 Gold")
    print("[2] Forged Sentinels - 10 Gold")
    print("[3] Keranjang - 50 Gold")
    print("[4] Upgrade Senjata (+5 ATK) - 45 Gold")
    print("[5] Keluar")
    choice = input("> ").strip()
    if choice == "1" and player.gold >= 15:
        player.gold -= 15
        player.potions += 1
        slow_print("Kamu membeli 1 Potion.")
    elif choice == "2" and player.gold >= 10:
        player.gold -= 10
        player.forged_sentinels = getattr(player, "forged_sentinels", 0) + 1
        slow_print("Kamu membeli 1 Forged Sentinels.")
    elif choice == "3" and player.gold >= 50:
        player.gold -= 50
        player.keranjang += 1
        slow_print("Kamu membeli 1 Keranjang.")
    elif choice == "4" and player.gold >= 45:
        player.gold -= 45
        player.attack += 5
        slow_print("Senjatamu ditingkatkan!")
    elif choice == "5":
        slow_print("Kamu meninggalkan toko.")
    else:
        slow_print("Uangmu tidak cukup atau pilihan tidak valid.")
    pause()

def handle__Gunung_Klotok_npc(player):
    slow_print("Warga Lokal berkata:")
    slow_print("Wanita: 'Jalanan itu sudah rusak.'")
    if player.job == "Pandhita wesi" and not player.is_Jayabaya:
        slow_print("Apakah kamu seorang Pandhita Wesi? Bisakah kamu menggendong saya melewati jalan itu?")
        slow_print("[1] Saya bisa saja.")
        slow_print("[2] Tolak permintaan — jalanan terlalu bahaya.")
        choice = input("> ").strip()
        if choice == "1":
            slow_print("Kamu menggendong gadis itu.")
            slow_print("Akan ada sedikit minigame — apakah kamu ingin melanjutkan?")
            slow_print("[Y] Lanjutkan  [N] Batal")
            yn = input("> ").strip().upper()
            if yn == "Y":
                slow_print("Mulai evolusi. Selesaikan urutan untuk membuka kekuatanmu.")
                ok = qte_sequence(steps=EVOLUTION_QTE_STEPS, timeout=QTE_TIMEOUT)
                if ok:
                    slow_print("Kamu berhasil membuka potential penuh mu.")
                    slow_print("Apa kamu mau berevolusi menjadi Raja Jayabaya? (Ini permanen.)")
                    slow_print("[Y] Ya, evolusi    [N] Tidak")
                    yn2 = input("> ").strip().upper()
                    if yn2 == "Y":
                        player.evolve_to_Jayabaya()
                        pause()
                    else:
                        slow_print("Kamu menolak evolusi. Kembali ke percakapan biasa.")
                        pause()
                else:
                    slow_print("Kamu gagal melewati tantangan itu, gadis itu kecewa.")
                    pause()
            else:
                slow_print("Kamu membatalkan percobaan evolusi.")
                pause()
        else:
            slow_print("Percakapan biasa berlangsung. Warga Lokal memberi petunjuk kecil.")
            pause()
    else:
        slow_print(random.choice([
            "\"Hati-hati — kamu bisa diserang di mana pun.\"",
            "\"Genderuwo itu sangat bahaya, waspadalah jika menemuinya.\"",
            "\"Simpang Lima Gumul sekarang kosong, banyak monster!\""
        ]))
        pause()

def enter_location(player, name):
    """Enter a location and stay inside until player chooses to go back to map."""
    loc = LOCATIONS[name]
    while True:
        clear()
        slow_print(f"=== {name} ===")
        slow_print(loc["desc"])
        if loc.get("npc"):
            slow_print(f"Warga Lokal: {loc['npc']}")
        print()
        if loc.get("artefact") and not loc["artefact_obtained"]:
            slow_print("Terdapat Artefact di sini. Kamu perlu 1 Keranjang untuk mengambilnya.")
        print("[1] Jelajahi area (bertemu musuh)")
        if loc.get("artefact") and not loc["artefact_obtained"]:
            print("[2] Mengambil Artefact (butuh 1 Keranjang)")
        print("[3] Bicara dengan Warga Lokal")
        print("[4] Kembali ke peta")

        choice = input("> ").strip()
        if choice == "1":
            enemy_data = random.choice(loc["enemies"])
            enemy = Enemy(*enemy_data)
            died = battle(player, enemy)
            if died:
                return "gameover"
        elif choice == "2" and loc.get("artefact") and not loc["artefact_obtained"]:
            if player.keranjang > 0:
                player.keranjang -= 1
                loc["artefact_obtained"] = True
                slow_print("Kamu berhasil mengambil artefact itu!")
                player.gold += 30
                player.add_exp(25)
                pause()
            else:
                slow_print("Tidak ada Keranjang.")
                pause()
        elif choice == "3":
            if name == "Gunung Klotok":
                handle__Gunung_Klotok_npc(player)
            else:
                slow_print("Warga Lokal berkata:")
                slow_print(random.choice([
                    "\"Hati hati- banyak siluman di sini.\"",
                    "\"Genderuwo itu sangat kuat, waspadalah jika menemuinya.\"",
                    "\"Hutan Joyoboyo sudah jadi sangat berbahaya...\""
                ]))
                pause()
        elif choice == "4":
            return "continue"
        else:
            slow_print("Pilihan tidak valid.")
            time.sleep(0.1)
            continue

def artefacts_obtained_count():
    return sum(1 for v in LOCATIONS.values() if v.get("artefact") and v["artefact_obtained"])

def Simpang_lima_gumul_encounter(player):
    clear()
    slow_print("Kamu telah tiba di Simpang Lima Gumul - Bangunan Sejarah Kerajaan Kediri.")
    if artefacts_obtained_count() < TOTAL_ARTEFACT:
        slow_print("Namun, kamu belum mengumpulkan semua artefact yang diperlukan untuk mengakses inti Simpang Lima Gumul.")
        pause()
        return False
    slow_print("Kamu telah mengumpulkan semua artefact! Tantangan terakhir muncul.")
    enemy_data = LOCATIONS["Simpang lima gumul"]["enemies"][0]
    enemy = Enemy(*enemy_data)
    enemy.can_burn = True
    died = battle(player, enemy)
    if died:
        return False
    slow_print("Kamu telah mengalahkan penjaga inti Simpang Lima Gumul.")
    pause()
    return True

def show_ending_victory(player):
    clear()
    slow_print("=== ENDING: Penebusan ===")
    slow_print("Dengan kebijaksanaan dan keberanianmu, semua pusaka kerajaan telah disucikan dan kekuatan suci Daha dipulihkan.")
    slow_print("Kerajaan Kediri terselamatkan; sungai Brantas kembali mengalir jernih dan roh para leluhur bersemayam dengan tenang.")
    slow_print("Namamu diabadikan dalam kidung para pujangga sebagai Sang Ksatria Penyelamat Negeri.")
    pause()

def show_ending_death():
    clear()
    slow_print("=== ENDING: Lenyap dalam Waktu ===")
    slow_print("Cahaya kerajaan meredup. Candi-candi runtuh tanpa penjaga, dan roh jahat menelan sisa peradaban.")
    slow_print("Kisahmu berakhir tragis; nama dan jasamu terkubur dalam debu sejarah Kediri.")
    pause()

def show_ending_Jayabaya(player):
    clear()
    slow_print("=== ENDING: Keagungan Abadi (Rahasia) ===")
    slow_print("Melampaui batas manusia, kamu menjelma menjadi Sang Hyang Penjaga Dharma.")
    slow_print("Dengan kesadaran suci dan kekuatan para dewa, kamu menjaga keseimbangan dunia dari alam arwah.")
    slow_print("Ending rahasia tercapai: hanya sedikit pujangga yang mengetahui kisahmu yang sesungguhnya.")
    pause()

def main():
    clear()
    slow_print(f"=== {GAME_TITLE} ===\n", 0.001)
    for line in INTRO:
        slow_print(line)
    pause()
    clear()
    name = input("Masukkan nama karaktermu: ").strip() or "Tekno"
    slow_print("\nPilih job:")
    print("[1] Kesatria panji (+200 HP, 25 ATK)")
    print("[2] Brahmana janggala (+125 HP, 40 ATK)")
    print("[3] Pemanah daha (+100 HP, 30 ATK)")
    print("[4] Pandhita wesi (+100 HP, 25 ATK)")
    job_choice = input("> ").strip()
    job = {"1": "Kesatria panji", "2": "Brahmana janggala", "3": "Pemanah daha", "4": "Pandhita wesi"}.get(job_choice, "Adventurer")
    player = Player(name, job)
    if name.strip().lower() == "dev":
        dev_mode(player)

    slow_print(f"\nSelamat datang, {player.name} sang {player.job}.")
    slow_print(f"Ambillah semua artefact ({TOTAL_ARTEFACT}) dan pulihkan Kediri!.")
    pause()

    while True:
        clear()
        slow_print("=== PETA Kediri ===")
        for i, loc in enumerate(LOCATIONS.keys(), start=1):
            loc_obj = LOCATIONS[loc]
            status = ""
            if loc_obj.get("artefact") and loc_obj["artefact_obtained"]:
                status = " (Artefact: 0)"
            elif loc_obj.get("artefact"):
                status = " (Artefact: X)"
            slow_print(f"[{i}] {loc}{status}")
        slow_print(f"[S] Toko  [C] Status  [Q] Keluar")

        choice = input("\nPilih: ").strip().upper()
        if choice == "Q":
            slow_print("Sampai jumpa, Prajurit.")
            break
        elif choice == "S":
            shop(player)
        elif choice == "C":
            clear()
            slow_print(f"=== Status {player.name} ===")
            slow_print(f"Job: {player.job} | Lv {player.level} | HP: {player.hp}/{player.max_hp}")
            slow_print(f"ATK: {player.attack} | Gold: {player.gold}")
            slow_print(f"Potion: {player.potions} | Keranjang: {player.keranjang}")
            slow_print(f"Artefact diperoleh: {artefacts_obtained_count()}/{TOTAL_ARTEFACT}")
            if player.job == "Pandhita wesi" or player.is_Jayabaya:
                slow_print(f"Forged Sentinels inventory: {player.forged_sentinels} | Active turns: {player.forged_sentinels_active_turns}")
            if player.is_Jayabaya:
                slow_print("Status: Raja Jayabaya (evolusi)")
            pause()
        else:
            try:
                idx = int(choice) - 1
                loc_names = list(LOCATIONS.keys())
                loc_name = loc_names[idx]
            except Exception:
                slow_print("Pilihan tidak valid.")
                time.sleep(0.1)
                continue
            if loc_name == "Simpang lima gumul":
                completed = Simpang_lima_gumul_encounter(player)
                if completed:
                    show_ending_victory(player)
                    if player.is_Jayabaya:
                        show_ending_Jayabaya(player)
                    break
                else:
                    if player.hp <= 0:
                        show_ending_death()
                        break
            else:
                result = enter_location(player, loc_name)
                if result == "gameover":
                    show_ending_death()
                    break

if __name__ == "__main__":
    main()
