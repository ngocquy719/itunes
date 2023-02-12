from pywinauto.application import Application
import pywinauto.keyboard as Keyboard
from faker import Faker
from phone_gen import PhoneNumber
import re, threading, time, subprocess, os, ctypes, datetime, random, string

country = [
    'Afghanistan',
    'Albania',
    'Algeria',
    'Angola',
    'Anguilla',
    'Antigua And Barbuda',
    'Argentina',
    'Armenia',
    'Australia',
    'Austria',
    'Azerbaijan',
    'Bahamas',
    'Bahrain',
    'Barbados',
    'Belarus',
    'Belgium',
    'Belize',
    'Benin',
    'Bermuda',
    'Bhutan',
    'Bolivia',
    'Bosnia and Herzegovina',
    'Botswana',
    'Brazil',
    'British Virgin Islands',
    'Brunei',
    'Bulgaria',
    'Burkina Faso',
    'Cambodia',
    'Cameroon',
    'Canada',
    'Cape Verde',
    'Cayman Islands',
    'Chad',
    'Chile',
    'China mainland',
    'Colombia',
    'Congo, Democratic',
    'Congo, Republic',
    'Costa Rica',
    'Cote d Ivoire',
    'Croatia',
    'Cyprus',
    'Czechia',
    'Denmark',
    'Dominica',
    'Dominican Republic',
    'Ecuador',
    'Egypt',
    'El Salvador',
    'Estonia',
    'Eswatini',
    'Fiji',
    'Finland',
    'France',
    'Gabon',
    'Gambia',
    'Georgia',
    'Germany',
    'Ghana',
    'Greece',
    'Grenada',
    'Guatemala',
    'Guinea-Bissau',
    'Guyana',
    'Honduras',
    'Hong Kong',
    'Hungary',
    'Iceland',
    'India',
    'Indonesia',
    'Iraq',
    'Ireland',
    'Israel',
    'Italy',
    'Jamaica',
    'Japan',
    'Jordan',
    'Kazakhstan',
    'Kenya',
    'Korea, Republic of',
    'Kosovo',
    'Kuwait',
    'Kyrgyzstan',
    'Laos',
    'Latvia',
    'Lebanon',
    'Liberia',
    'Libya',
    'Lithuania',
    'Luxembourg',
    'Macao',
    'Madagascar',
    'Malawi',
    'Malaysia',
    'Maldives',
    'Mali',
    'Malta',
    'Mauritania',
    'Mauritius',
    'Mexico',
    'Micronesia',
    'Moldova',
    'Mongolia',
    'Montenegro',
    'Montserrat',
    'Morocco',
    'Mozambique',
    'Myanmar',
    'Namibia',
    'Nauru',
    'Nepal',
    'Netherlands',
    'New Zealand',
    'Nicaragua',
    'Niger',
    'Nigeria',
    'North Macedonia',
    'Norway',
    'Oman',
    'Pakistan',
    'Palau',
    'Panama',
    'Papua New Guinea',
    'Paraguay',
    'Peru',
    'Philippines',
    'Poland',
    'Portugal',
    'Qatar',
    'Romania',
    'Russia',
    'Rwanda',
    'Sao Tome And Principe',
    'Saudi Arabia',
    'Senegal',
    'Serbia',
    'Seychelles',
    'Sierra Leone',
    'Singapore',
    'Slovakia',
    'Slovenia',
    'Solomon Islands',
    'South Africa',
    'Spain',
    'Sri Lanka',
    'St Kitts And Nevis',
    'St Lucia',
    'St Vincent and the Grenadines',
    'Suriname',
    'Sweden',
    'Switzerland',
    'Taiwan',
    'Tajikistan',
    'Tanzania',
    'Thailand',
    'Tonga',
    'Trinidad and Tobago',
    'Tunisia',
    'Turkey',
    'Turkmenistan',
    'Turks and Caicos Islands',
    'Uganda',
    'Ukraine',
    'United Arab Emirates',
    'United Kingdom',
    'United States',
    'Uruguay',
    'Uzbekistan',
    'Vanuatu',
    'Venezuela',
    'Vietnam',
    'Yemen',
    'Zambia',
    'Zimbabwe'
]

states = [
	['Alabama', 'AL', '35004'],
	['Alaska', 'AK', '99502'],
	['Arizona', 'AZ', '85018'],
	['Arkansas', 'AR', '72204'],
	['California', 'CA', '90210'],
	['Colorado', 'CO', '80202'],
	['Connecticut', 'CT', '06052'],
	['Delaware', 'DE', '19808'],
	['Florida', 'FL', '32244'],
	['Georgia', 'GA', '30004'],
	['Hawaii', 'HI', '96815'],
	['Idaho', 'ID', '83616'],
	['Illinois', 'IL', '60601'],
	['Indiana', 'IN', '46001'],
	['Iowa', 'IA', '52220'],
	['Kansas', 'KS', '66012'],
	['Kentucky', 'KY', '41042'],
	['Louisiana', 'LA', '70130'],
	['Maine', 'ME', '04240'],
	['Maryland', 'MD', '21234'],
	['Massachusetts', 'MA', '02148'],
	['Michigan', 'MI', '49503'],
	['Minnesota', 'MN', '55082'],
	['Mississippi', 'MS', '38654'],
	['Missouri', 'MO', '63376'],
	['Montana', 'MT', '59102'],
	['Nebraska', 'NE', '68104'],
	['Nevada', 'NV', '89108'],
	['New Hampshire', 'NH', '03102'],
	['New Jersey', 'NJ', '08701'],
	['New Mexico', 'NM', '87154'],
	['New York', 'NY', '11368'],
	['North Carolina', 'NC', '27006'],
	['North Dakota', 'ND', '58104'],
	['Ohio', 'OH', '43004'],
	['Oklahoma', 'OK', '74012'],
	['Oregon', 'OR', '97215'],
	['Pennsylvania', 'PA', '16820'],
	['Rhode Island', 'RI', '02840'],
	['South Carolina', 'SC', '29418'],
	['South Dakota', 'SD', '57702'],
	['Tennessee', 'TN', '37012'],
	['Texas', 'TX', '75014'],
	['Utah', 'UT', '84108'],
	['Vermont', 'VT', '05452'],
	['Virginia', 'VA', '23464'],
	['Washington', 'WA', '98102'],
	['West Virginia', 'WV', '26554'],
	['Wisconsin', 'WI', '53233'],
	['Wyoming', 'WY', '82001']
]

def is_admin():
    try:
        if ctypes.windll.shell32.IsUserAnAdmin():
            print('is_admin >>> true')
        else:
            print('is_admin >>> false')
    except:
        print('is_admin >>> false')

def next_alpha(s):
    return chr((ord(s.upper())+1 - 65) % 26 + 65)

def save_to_file(file_name, line):
    text = '%s\n' % (line)
    print('save-to-file >>> ', text[:-1])
    with open(file_name, 'a+') as (file):
        file.write(text)

def delete_line(original_file, line_number):
    is_skipped = False
    current_index = 0
    dummy_file = original_file + '.bak'
    with open(original_file, 'r') as read_obj, open(dummy_file, 'w') as write_obj:
        for line in read_obj:
            if current_index != line_number:
                write_obj.write(line)
            else:
                is_skipped = True
            current_index += 1
    
    if is_skipped:
        os.remove(original_file)
        os.rename(dummy_file, original_file)
    else:
        os.remove(dummy_file)

def waiting_app(app):
    print('waiting_app')
    while 1:
        if len(app.windows()) > 0:
            print('app ok')
            break
        time.sleep(0.5)
        #print('still waiting...')

class Worker(threading.Thread):

    def read_file(self, filename):
        with open(filename) as (file):
            codes = file.readlines()
        return codes
    
    def terminate(self):
        print('stopped')
        
    def run(self):
        accs_filename = 'accs-United-States-doing.txt'
        cards_filename = 'cards-US-doing.txt'
        today = datetime.date.today()
        d1 = today.strftime("%d_%m_%Y")
        country_code = "US"
        country_name = "United States"
        locale = 'en_US'
        acc_ok_log_file = 'accs_ok_%s_%s_%s.txt' % (os.path.splitext(accs_filename)[0], country_code, d1)
        card_ok_log_file = 'cards_ok_%s_%s_%s.txt' % (os.path.splitext(cards_filename)[0], country_code, d1)
        error_log_file = 'error_%s.txt' % (country_code)
        accs = self.read_file(accs_filename)
        cards = self.read_file(cards_filename)
        num_run = len(accs)
        num_card = len(cards)
        run_count = 0
        card_count = 0
        num_card_check_one_acc = 10
        is_admin()
        clear_bat_file_path = os.path.abspath('clear_itune.bat')
        kill_bat_file_path = os.path.abspath('kill_itune.bat')
        in_review = False
        change_store = True
        store_dialog = True
        
        while 1:
            if run_count == num_run:
                print('finish - out of acc')
                break
            in_review = False
            
            print('>>>>>>>>>>>>>>>>>>> kill iTunes <<<<<<<<<<<<<<<<<<<<<<<')
            process = subprocess.Popen(r'%s' %(kill_bat_file_path), shell=True, stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)
            process.communicate()
            
            try:
                print('starting random info ...')
                app = Application(backend="uia").start(r"random_info.exe")
                waiting_app(app)
                app_random_dialog = app['一键修改系统信息']
                app_random_dialog.wait('ready', timeout=5)
                app_random_dialog.set_focus()
                app_random_dialog.child_window(title="RandALL", control_type="Button").click()
                time.sleep(1)
                app_random_dialog.child_window(title="OK", control_type="Button").wait('ready', timeout=5)
                app_random_dialog.child_window(title="OK", control_type="Button").click()
                time.sleep(1)
                app_random_success_dialog = app_random_dialog.child_window(title="提示", control_type="Window")
                app_random_success_dialog.wait('ready', timeout=10)
                app_random_success_dialog.child_window(title="OK", control_type="Button").click()
                app_random_success_dialog.wait_not('visible')
                app.kill()
                print('random info successful')
            except Exception:
                print('random info error')
            
            print('clearing iTunes ...')
            process = subprocess.Popen(r'%s' %(clear_bat_file_path), shell=True, stdout=subprocess.DEVNULL, stderr=subprocess.DEVNULL)
            process.communicate()
            
            print('starting iTunes ...')
            app = Application(backend="uia").start(r"C:\Program Files\iTunes\iTunes.exe")
            waiting_app(app)
            
            try:
                app_first_dialog = app['iTunes Software License Agreement']
                app_first_dialog.wait('ready', timeout=5)
                app_first_dialog.Agree.click()
                waiting_app(app)
            except Exception:
                print('first dialog is not exits')
            
            app_dialog = app['iTunes']
            
            app_dialog.wait('ready', timeout=20)
            app_dialog.set_focus()
            try:
                download_dialog = app_dialog.window(title='iTunes')
                download_dialog.wait('ready', timeout=15)
                download_dialog.set_focus()
                download_dialog.Button2.click()
                print('click dont download')
                download_dialog.wait_not('visible')
            except Exception:
                print('download dialog is not exits')
                        
            app_dialog.set_focus()
            Keyboard.send_keys('%A')
            Keyboard.send_keys('S')
            
            acc = accs[run_count].strip()
            tmp = acc.split(':')
            while len(tmp) != 2:
                delete_line(accs_filename, 0)
                run_count = run_count + 1
                print('run acc ', run_count, '/', num_run, ' >>  format error')
                if run_count == num_run:
                    break
                acc = accs[run_count].strip()
                tmp = acc.split(':')
            if run_count == num_run:
                print('finish - out of acc')
                break
            username = tmp[0]
            password = tmp[1]
            print('run acc ', run_count + 1, '/', num_run, ' >> ', acc)
            
            try:
                signin_dialog = app_dialog.window(title='iTunes')
                signin_dialog.wait('ready', timeout=10)
                signin_dialog.set_focus()
                signin_dialog.Edit.type_keys(username)
                signin_dialog.Edit2.type_keys(password)
                signin_dialog.child_window(title="Sign In").click()
                signin_dialog.wait_not('visible', timeout=30)
                delete_line(accs_filename, 0)
                run_count = run_count + 1
            except Exception as e:
                print('signin error')
                app_dialog.set_focus()
                debug_image = app_dialog.capture_as_image()
                debug_image.save('logs/' + username + '_' + str(int(datetime.datetime.now().timestamp())) + '.png')
                save_to_file(error_log_file, acc)
                print(e)
                if (app_dialog.child_window(title="Unable to sign you in to your Apple ID. Try again later.").exists(timeout=0.5) or
                    app_dialog.child_window(title="There was an error connecting to the Apple ID server.").exists(timeout=0.5)):
                    print('try signin again')
                    continue
                delete_line(accs_filename, 0)
                run_count = run_count + 1
                continue
            
            if store_dialog:
                try:
                    store_dialog = app_dialog.window(title='iTunes')
                    store_dialog.wait('ready', timeout=20)
                    store_dialog.set_focus()
                    if store_dialog.child_window(title="Review", control_type="Button").exists(timeout=1):
                        print('review dialog is shown')
                        in_review = True
                        store_dialog.child_window(title="Review", control_type="Button").click()
                    else:
                        print('store dialog is shown')
                        in_review = False
                        label = store_dialog.child_window(control_type="Text").window_text()
                        storename_str = re.findall("\n[a-zA-Z ]+.\n", label)[0].strip()
                        storename_str = storename_str[:-1]
                        print('store >>> ', storename_str)
                        store_dialog.OK.click()
                        print('store dialog - click ok')
                        store_dialog.wait_not('visible')
                    if not change_store and storename_str != country_name:
                        save_to_file("%s.txt" % storename_str, acc)
                        continue
                except Exception:
                    print('store dialog is not exits')
            
            fake = Faker(locale)
            if in_review:
                print('in_review')
                try:
                    app_dialog.child_window(title="No Thanks").click()
                    app_dialog.child_window(title="No Thanks").wait_not('visible')
                    app_dialog.set_focus()
                    Keyboard.send_keys('%A')
                    Keyboard.send_keys('S')
                    signin_dialog = app_dialog.window(title='iTunes')
                    signin_dialog.wait('ready', timeout=10)
                    signin_dialog.set_focus()
                    signin_dialog.Edit.type_keys(username)
                    signin_dialog.Edit2.type_keys(password)
                    signin_dialog.child_window(title="Sign In").click()
                    signin_dialog.wait_not('visible', timeout=30)
                    store_dialog = app_dialog.window(title='iTunes')
                    store_dialog.wait('ready', timeout=20)
                    store_dialog.set_focus()
                    store_dialog.child_window(title="Review", control_type="Button").click()
                    try:
                        app_dialog.child_window(title="Continue").wait('ready', timeout=20)
                        app_dialog.child_window(title="Continue").click()
                        app_dialog.child_window(title="Continue").wait_not('visible')
                    except Exception:
                        print('Continue button dont show')
                
                    app_dialog.child_window(title="Complete your Apple ID", found_index=0).wait('ready', timeout=60)
                    app_dialog.set_focus()
                    app_dialog.click_input(coords=(164, 148))
                    time.sleep(0.5)
                    Keyboard.send_keys('{TAB}')
                    
                    result = [i for i in country if i.startswith(country_name[0])]
                    step_move = result.index(country_name) + 1
                    Keyboard.send_keys(next_alpha(country_name[0]))
                    app_dialog.child_window(title="Complete your Apple ID", found_index=1).wait('ready', timeout=10)
                    for m in range(step_move):
                        Keyboard.send_keys(country_name[0])
                        app_dialog.child_window(title="Complete your Apple ID", found_index=1).wait('ready', timeout=10)
                    Keyboard.send_keys('{TAB}')
                    Keyboard.send_keys('{SPACE}')
                    
                    app_dialog.child_window(title="Continue", control_type="Button").click()
                    time.sleep(2)
                    
                    app_dialog.child_window(title="Billing Address", found_index=0).wait('ready', timeout=20)
                    app_dialog.set_focus()
                    app_dialog.click_input(coords=(164, 148))
                    Keyboard.send_keys('{TAB 9}')
                    time.sleep(0.5)                    
                    Keyboard.send_keys(fake.street_address(), with_spaces=True)
                    time.sleep(0.5)
                    Keyboard.send_keys('{TAB}')
                    Keyboard.send_keys(fake.secondary_address(), with_spaces=True)
                    time.sleep(0.5)
                    Keyboard.send_keys('{TAB}')
                    Keyboard.send_keys(fake.city(), with_spaces=True)
                    time.sleep(0.5)
                    
                    Keyboard.send_keys('{TAB}')
                    s = random.randint(1, 50)
                    state_name = states[s][0]
                    result = [i[0] for i in states if i[0].startswith(state_name[0])]
                    step_move = result.index(state_name) + 1
                    for m in range(step_move):
                        Keyboard.send_keys(state_name[0])
                        time.sleep(0.02)
                    Keyboard.send_keys('{ENTER}')
                    time.sleep(0.5)
                    
                    Keyboard.send_keys('{TAB}')
                    Keyboard.send_keys(states[s][2])
                    time.sleep(0.5)
                    Keyboard.send_keys('{TAB}')
                    Keyboard.send_keys("%03d" % random.randint(200, 999))
                    time.sleep(0.5)
                    Keyboard.send_keys('{TAB}')
                    phone = ''.join(random.choice(string.digits) for i in range(7))
                    Keyboard.send_keys(phone)
                    time.sleep(0.5)
                    Keyboard.send_keys('{TAB 3}')
                    
                    app_dialog.child_window(title="Continue", control_type="Button").click()
                    time.sleep(5)
                    
                    app_dialog.child_window(title="Apple ID Completed", found_index=1).wait('ready', timeout=20)
                    print('apple id completed')
                    app_dialog.set_focus()
                    app_dialog.child_window(title="Continue", control_type="Button").click()
                    time.sleep(5)
                    Keyboard.send_keys('%A')
                    Keyboard.send_keys('V')
                    time.sleep(5)
                    
                    app_dialog.child_window(title="Cancel", control_type="Button").wait('ready', timeout=20)
                    Keyboard.send_keys(password)
                    time.sleep(0.5)
                    app_dialog.child_window(title="Sign In", control_type="Button").click()
                    app_dialog.child_window(title="Sign In", control_type="Button").wait_not('visible', timeout=20)
                    
                    app_dialog.child_window(title="Account Information", found_index=0).wait('ready', timeout=30)
                    print('review store %s successful' %(country_name))
                    save_to_file(acc_ok_log_file, acc)
                    
                except Exception as e:
                    print('review error')
                    app_dialog.set_focus()
                    debug_image = app_dialog.capture_as_image()
                    debug_image.save('logs/' + username + '_' + str(int(datetime.datetime.now().timestamp())) + '.png')
                    save_to_file(error_log_file, acc)
                    print(e)
                    continue
            else:
                try:
                    app_dialog.child_window(title="No Thanks").click()
                    app_dialog.child_window(title="No Thanks").wait_not('visible')
                    time.sleep(5)
                    Keyboard.send_keys('%A')
                    Keyboard.send_keys('V')
                    try:
                        app_dialog.child_window(title="Continue").wait('ready', timeout=20)
                        app_dialog.child_window(title="Continue").click()
                        app_dialog.child_window(title="Continue").wait_not('visible')
                    except Exception:
                        print('Continue button dont show')
                    app_dialog.child_window(title="Account Information", found_index=0).wait('ready', timeout=60)
                    save_to_file(acc_ok_log_file, acc)
                    if change_store:
                        print('change_store >>> ', change_store)
                        if storename_str == country_name:
                            print('do not need to change store')
                        else:
                            app_dialog.child_window(title="Change Country or Region", control_type="Hyperlink").invoke()
                            app_dialog.child_window(title="Select a country or region").wait('ready', timeout=20)
                            app_dialog.set_focus()
                            app_dialog.click_input(coords=(338, 320))
                            time.sleep(1)
                            
                            storename_change_str = country_name
                            index1 = country.index(storename_str)
                            index2 = country.index(storename_change_str)
                            if (index1 < index2):
                                step_move= index2 - index1
                                for m in range(step_move):
                                    Keyboard.send_keys('{DOWN}')
                                    time.sleep(0.02)
                            else:
                                step_move= index1 - index2
                                for m in range(step_move):
                                    Keyboard.send_keys('{UP}')
                                    time.sleep(0.02)
                            Keyboard.send_keys('{ENTER}')
                            time.sleep(0.5)
                            
                            try:
                                app_dialog.child_window(title="Terms and Conditions", found_index=1).wait('ready', timeout=20)
                            except Exception:
                                app_dialog.child_window(title="Terms & Conditions", found_index=1).wait('ready', timeout=1)
                            Keyboard.send_keys('{END}')
                            time.sleep(0.5)
                            app_dialog.child_window(title="Agree", control_type="Button").click()
                            time.sleep(0.5)
                            Keyboard.send_keys('%A')
                            time.sleep(0.5)
                            
                            app_dialog.child_window(title="Review Payment Information", found_index=0).wait('ready', timeout=20)
                            app_dialog.child_window(title="None", control_type="RadioButton").click()
                            time.sleep(0.5)
                            app_dialog.set_focus()
                            app_dialog.click_input(coords=(334, 458))
                            time.sleep(0.5)
                            Keyboard.send_keys(fake.street_address(), with_spaces=True)
                            time.sleep(0.5)
                            Keyboard.send_keys('{TAB}')
                            Keyboard.send_keys(fake.secondary_address(), with_spaces=True)
                            time.sleep(0.5)
                            Keyboard.send_keys('{TAB}')
                            Keyboard.send_keys(fake.city(), with_spaces=True)
                            time.sleep(0.5)
							
                            Keyboard.send_keys('{TAB}')
                            s = random.randint(1, 50)
                            state_name = states[s][0]
                            result = [i[0] for i in states if i[0].startswith(state_name[0])]
                            step_move = result.index(state_name) + 1
                            for m in range(step_move):
                                Keyboard.send_keys(state_name[0])
                                time.sleep(0.02)
                            Keyboard.send_keys('{ENTER}')
                            time.sleep(0.5)
							
                            Keyboard.send_keys('{TAB}')
                            Keyboard.send_keys(states[s][2])
                            time.sleep(0.5)
                            Keyboard.send_keys('{TAB}')
                            Keyboard.send_keys("%03d" % random.randint(200, 999))
                            time.sleep(0.5)
                            Keyboard.send_keys('{TAB}')
                            phone = ''.join(random.choice(string.digits) for i in range(7))
                            Keyboard.send_keys(phone)
                            time.sleep(0.5)
                            Keyboard.send_keys('{TAB 3}')
                            app_dialog.child_window(title="Continue", control_type="Button").click()
                            time.sleep(0.5)
                            
                            app_dialog.child_window(title="Account Information", found_index=0).wait('ready', timeout=30)
                            print('change store %s successful' %(storename_change_str))
                    
                except Exception as e:
                    print('change store error')
                    app_dialog.set_focus()
                    debug_image = app_dialog.capture_as_image()
                    debug_image.save('logs/' + username + '_' + str(int(datetime.datetime.now().timestamp())) + '.png')
                    save_to_file(error_log_file, acc)
                    print(e)
                    continue
            
            try:    
                print('Account Information')
                app_dialog.child_window(title="Manage Payments", control_type="Hyperlink").invoke()
                for i in range(6):
                    try:
                        time.sleep(5)
                        app_dialog.set_focus()
                        app_dialog.click_input(coords=(164, 148))
                        Keyboard.send_keys('{HOME}')
                        time.sleep(0.5)
                        app_dialog.child_window(title="Add Payment", found_index=0).wait('ready', timeout=1)
                        break
                    except Exception:
                        pass
                else:
                    raise Exception('Add Payment load timeout')
                app_dialog.child_window(title="Visa", control_type="RadioButton").click()
                time.sleep(1)
                
                check_ok = False
                check_error = False
                month = '01'
                year = '2023'
                
                for ii in range(num_card_check_one_acc):
                    card = cards[card_count].strip()
                    tmp = card.split('|')
                    if (len(str(tmp[2])) == 2):
                        tmp[2] = '20' + tmp[2]
                    while not (len(tmp) >= 4 and 
                           (tmp[0].isdigit() and len(tmp[0]) == 16) and 
                           (tmp[1].isdigit() and int(tmp[1]) >= 1 and int(tmp[1]) <= 12) and
                           (tmp[2].isdigit() and int(tmp[2]) >= 2022 and int(tmp[2]) <= 2032) and
                           (tmp[3].isdigit() and len(str(tmp[3])) == 3) and
                           ((True if (int(tmp[2]) == 2022 and int(tmp[1]) >= 5) else False) or 
                           (True if (int(tmp[2]) > 2022) else False))):
                        delete_line(cards_filename, 0)
                        card_count = card_count + 1
                        print('card ', card_count, '/', num_card, ' >> ', card, ' >>  format error')
                        if card_count == num_card:
                            break
                        card = cards[card_count].strip()
                        tmp = card.split('|')
                    if card_count == num_card:
                        print('finish - out of card')
                        break
                    cardnumber = tmp[0]
                    last_month = month
                    month = tmp[1]
                    last_year = year
                    year = tmp[2]
                    ccv = tmp[3]
                    text = '%s|%s|%s|%s' % (cardnumber,month,year,ccv)
                    print('adding card ', ii + 1, '/', num_card_check_one_acc , '(', card_count + 1, '/', num_card, ')  >> ', text)
                    
                    app_dialog.set_focus()
                    app_dialog.click_input(coords=(164, 148))
                    Keyboard.send_keys('{TAB 6}')
                    time.sleep(0.5)
                    if check_error:
                        
                        Keyboard.send_keys(cardnumber)
                        Keyboard.send_keys('{TAB}')
                        time.sleep(0.5)
                        index1 = int(last_month)
                        index2 = int(month)
                        if (index1 < index2):
                            step_move= index2 - index1
                            for m in range(step_move):
                                Keyboard.send_keys('{DOWN}')
                                time.sleep(0.02)
                        else:
                            step_move= index1 - index2
                            for m in range(step_move):
                                Keyboard.send_keys('{UP}')
                                time.sleep(0.02)
                        Keyboard.send_keys('{ENTER}')
                        time.sleep(0.5)
                        Keyboard.send_keys('{TAB}')
                        time.sleep(0.5)
                        index1 = int(last_year)
                        index2 = int(year)
                        if (index1 < index2):
                            step_move= index2 - index1
                            for m in range(step_move):
                                Keyboard.send_keys('{DOWN}')
                                time.sleep(0.02)
                        else:
                            step_move= index1 - index2
                            for m in range(step_move):
                                Keyboard.send_keys('{UP}')
                                time.sleep(0.02)
                        Keyboard.send_keys('{ENTER}')
                        time.sleep(0.5)
                        Keyboard.send_keys('{TAB}')
                        time.sleep(0.5)
                        Keyboard.send_keys(ccv)
                        time.sleep(0.5)
                    else:
                        Keyboard.send_keys(cardnumber)
                        Keyboard.send_keys('{TAB}')
                        time.sleep(0.5)
                        for m in range(int(month)):
                            Keyboard.send_keys('{DOWN}')
                            time.sleep(0.02)
                        Keyboard.send_keys('{ENTER}')
                        time.sleep(0.5)
                        Keyboard.send_keys('{TAB}')
                        time.sleep(0.5)
                        for m in range(int(year) - 2022):
                            Keyboard.send_keys('{DOWN}')
                            time.sleep(0.02)
                        Keyboard.send_keys('{ENTER}')
                        time.sleep(0.5)
                        Keyboard.send_keys('{TAB}')
                        time.sleep(0.5)
                        Keyboard.send_keys(ccv)
                        time.sleep(0.5)
                    Keyboard.send_keys('{TAB 3}')
                    time.sleep(0.5)
                    Keyboard.send_keys(fake.street_address(), with_spaces=True)
                    time.sleep(0.5)
                    Keyboard.send_keys('{TAB}')
                    Keyboard.send_keys(fake.secondary_address(), with_spaces=True)
                    time.sleep(0.5)
                    Keyboard.send_keys('{TAB 4}')
                    time.sleep(0.5)
                    Keyboard.send_keys("%03d" % random.randint(200, 999))
                    time.sleep(0.5)
                    Keyboard.send_keys('{TAB}')
                    phone = ''.join(random.choice(string.digits) for i in range(7))
                    Keyboard.send_keys(phone)
                    time.sleep(0.5)
                    Keyboard.send_keys('{TAB}')
                    time.sleep(0.5)
                    app_dialog.child_window(title="Done", control_type="Button").click()
                    time.sleep(5)
                    check_ok = False
                    check_error = False
                    app_dialog.set_focus()
                    while not (check_ok or check_error):
                        try:
                            app_dialog.child_window(title="Billing Address", found_index=0).wait('ready', timeout=0.5)
                            check_error = True
                        except Exception:
                            try:
                                app_dialog.child_window(title="Add Payment Method").wait('ready', timeout=0.5)
                                check_ok = True
                            except Exception:
                                pass
                    
                    if check_error:
                        if app_dialog.child_window(title="Cancel", control_type="Button").exists(timeout=1):
                            app_dialog.child_window(title="Cancel", control_type="Button").click()
                            print('can not add card >>> ver bank')
                            delete_line(cards_filename, 0)
                            card_count = card_count + 1
                        else:
                            label = app_dialog.child_window(control_type="ListItem", found_index=0).window_text()
                            print('can not add card >>> ', label)
                            label = ''.join(label.split())
                            if 'ContactAppleSupportformoreinformation' in label:
                                print('support >>> change acc')
                                break
                                print('support >>> try one more time')
                                app_dialog.click_input(coords=(519, 365 + 70))
                                time.sleep(0.5)
                                Keyboard.send_keys('{BACKSPACE}')
                                time.sleep(0.5)
                                app_dialog.click_input(coords=(519, 400 + 70))
                                time.sleep(0.5)
                                Keyboard.send_keys('{END}')
                                time.sleep(0.5)
                                app_dialog.child_window(title="Done", control_type="Button").click()
                                time.sleep(5)
                                app_dialog.child_window(title="Billing Address", found_index=0).wait('ready', timeout=20)
                                app_dialog.click_input(coords=(519, 365 + 70))
                                time.sleep(0.5)
                                Keyboard.send_keys('^A')
                                Keyboard.send_keys('{DELETE}')
                                Keyboard.send_keys(ccv)
                                app_dialog.click_input(coords=(519, 400 + 70))
                                time.sleep(0.5)
                                Keyboard.send_keys('{END}')
                                time.sleep(0.5)
                                app_dialog.child_window(title="Done", control_type="Button").click()
                                time.sleep(5)
                                check_ok = False
                                check_error = False
                                while not (check_ok or check_error):
                                    try:
                                        app_dialog.child_window(title="Billing Address", found_index=0).wait('ready', timeout=0.5)
                                        check_error = True
                                    except Exception:
                                        try:
                                            app_dialog.child_window(title="Add Payment Method").wait('ready', timeout=0.5)
                                            check_ok = True
                                        except Exception:
                                            pass
                                if check_error:
                                    label = app_dialog.child_window(control_type="ListItem", found_index=0).window_text()
                                    print('can not add card >>> ', label)
                                    label = ''.join(label.split())
                                    if 'ContactAppleSupportformoreinformation' in label:
                                        print('still support >>> change acc')
                                        break
                                    else:
                                        delete_line(cards_filename, 0)
                                        card_count = card_count + 1
                            else:
                                delete_line(cards_filename, 0)
                                card_count = card_count + 1
                    if check_ok:
                        print('add card ok >>> ', text, " <<<")
                        app_dialog.set_focus()
                        app_dialog.child_window(title="Edit", control_type="Button", found_index=0).wait('ready', timeout=20)
                        Keyboard.send_keys('%A')
                        Keyboard.send_keys('V')
                        time.sleep(5)
                        app_dialog.child_window(title="Manage Payments", control_type="Hyperlink").invoke()
                        app_dialog.child_window(title="Edit", control_type="Button", found_index=0).wait('ready', timeout=20)
                        app_dialog.child_window(title="Edit", control_type="Button", found_index=0).click()
                        app_dialog.child_window(title="Edit", control_type="Button", found_index=0).wait_not('visible')
                        time.sleep(1)
                        Keyboard.send_keys('{END}')
                        time.sleep(0.5)
                        app_dialog.child_window(title="Remove Payment Method", control_type="Button").wait('ready', timeout=20)
                        app_dialog.child_window(title="Remove Payment Method", control_type="Button").click()
                        time.sleep(5)
                        app_dialog.child_window(title="Add Payment Method").wait('ready', timeout=40)
                        delete_line(cards_filename, 0)
                        save_to_file(card_ok_log_file, card)
                        card_count = card_count + 1
                        if ii < num_card_check_one_acc - 1:
                            Keyboard.send_keys('%A')
                            Keyboard.send_keys('V')
                            time.sleep(5)
                            app_dialog.child_window(title="Manage Payments", control_type="Hyperlink").invoke()
                            for i in range(6):
                                try:
                                    time.sleep(5)
                                    app_dialog.set_focus()
                                    app_dialog.click_input(coords=(164, 148))
                                    Keyboard.send_keys('{HOME}')
                                    time.sleep(0.5)
                                    app_dialog.child_window(title="Add Payment", found_index=0).wait('ready', timeout=1)
                                    break
                                except Exception:
                                    pass
                            else:
                                raise Exception('Add Payment load timeout')
                            app_dialog.child_window(title="Visa", control_type="RadioButton").click()
                            time.sleep(1)
                    
                    if card_count == num_card:
                        print('finish - out of card')
                        break
                
                if card_count == num_card:
                    break
                print('done >>> next acc')
                
            except Exception as e:
                print('account information error')
                app_dialog.set_focus()
                debug_image = app_dialog.capture_as_image()
                debug_image.save('logs/' + username + '_' + str(int(datetime.datetime.now().timestamp())) + '.png')
                save_to_file(error_log_file, acc)
                print(e)
                continue
        
        
        
        self.terminate()

def clicked():
    print('clicked')
    global w 
    w = Worker()
    w.start()
clicked()
