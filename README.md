from random import *
import time

def main():
   games_played = 0
   wins = 0
   while True:
      games_played += 1
      result = play()
      if result:
         wins += 1
      print(f'\n🔁 Игр сыграно: {games_played}, Побед: {wins}')
      if input("Хочешь сыграть снова? (да/нет): ").lower() != 'да':
         print("Спасибо за игру!")
         break

def print_game_state(tries, word_completion, guessed_letters):
   print(display_hangman(tries))
   print('Слово:', word_completion)
   print('Угаданные буквы:', ' '.join(guessed_letters))
   print()

def display_hangman(tries):
    stages = [# финальное состояние: голова, торс, обе руки, обе ноги
                '''
                   --------
                   |      |
                   |      O
                   |     \\|/
                   |      |
                   |     / \\
                   -
                ''',
                # голова, торс, обе руки, одна нога
                '''
                   --------
                   |      |
                   |      O
                   |     \\|/
                   |      |
                   |     / 
                   -
                ''',
                # голова, торс, обе руки
                '''
                   --------
                   |      |
                   |      O
                   |     \\|/
                   |      |
                   |      
                   -
                ''',
                # голова, торс и одна рука
                '''
                   --------
                   |      |
                   |      O
                   |     \\|
                   |      |
                   |     
                   -
                ''',
                # голова и торс
                '''
                   --------
                   |      |
                   |      O
                   |      |
                   |      |
                   |     
                   -
                ''',
                # голова
                '''
                   --------
                   |      |
                   |      O
                   |    
                   |      
                   |     
                   -
                ''',
                # начальное состояние
                '''
                   --------
                   |      |
                   |      
                   |    
                   |      
                   |     
                   -
                '''
    ]
    return stages[tries]

def play():
   categories = {
    'предметы': ['стол', 'книга', 'телефон', 'чайник', 'карандаш', 'окно', 'дверь', 'пальто'],
    'животные': ['кошка', 'собака', 'змея', 'тигр', 'мышь', 'лошадь', 'медведь'],
    'природа': ['гора', 'река', 'море', 'облако', 'небо', 'звезда', 'дерево'],
    'транспорт': ['поезд', 'самолёт', 'автобус', 'машина'],
    'люди': ['человек', 'учитель', 'друг', 'студент', 'врач'],
    'еда': ['яблоко', 'банан', 'груша', 'арбуз', 'торт', 'пирог']
}

   while True:
      tries = 6
      hints_left = 2
      guessed = False
      guessed_words = []
      guessed_letters = []
      
      print('\nДоступные категории:')
      for cat in categories:
         print(f'📂 {cat}')
      chosen_category = input('Выберите категорию: ').strip().lower()

      if chosen_category not in categories:
         print('❗ Такой категории нет. Выбрана случайная категория.')
         chosen_category = choice(list(categories.keys()))
      local_list = categories[chosen_category]

      print(f'🔍 Выбранная категория: {chosen_category.capitalize()}')
      
      while True:
         difficulty = input('Выбери уровень: лёгкий, средний, трудный: ').lower()
         if difficulty[0] == 'л':
            filtered = [w for w in local_list if len(w) < 5]
         elif difficulty[0] == 'т':
            filtered = [w for w in local_list if len(w) > 7]
         elif difficulty[0] == 'с':
            filtered = [w for w in local_list if 5 <= len(w) <= 7]
            break
         else:
            print('❗ Неверный ввод. Попробуйте снова.')
            continue
      
         if filtered:
            local_list = filtered
            break
         else:
            print('❗ Нет слов с таким уровнем сложности. Будет выбрано слово из всей категории.')
            break
         
      word = choice(local_list).upper()
      word_completion = '_' * len(word)
   
      print(display_hangman(tries))
      print(f'Загадано слово из {len(word)} букв: {word_completion}')
      print(f'У вас {tries} попыток. Введите "?" для подсказки (максимум {hints_left} раз).')
      start_time = time.time()
   
      while not guessed and tries > 0:
         guess = input('Введите букву или слово целиком (или "?" для подсказки, "категория" для смены): ').strip().upper()
         
         if guess == '?':
            if hints_left == 0:
               print('❌ Подсказки закончились!')
               continue
            hint_letters = [l for l in word if l not in guessed_letters]
            if hint_letters:
               hint_letters = choice(hint_letters)
               print(f'🔎 Подсказка: буква "{hint_letters}" есть в слове. Осталось подсказок: {hints_left}')
               guessed_letters.append(hint_letters)
               word_completion = ''.join([letter if letter in guessed_letters else '_' for letter in word])
               hints_left -= 1
            else:
               print('Нет доступных подсказок.')
            print_game_state(tries, word_completion)
            continue
         
         if guess.lower() == 'категория':
                return play()
            
         if not guess.isalpha():
               print('❗ Вводите только буквы или слова.')
               continue
            
         if len(guess) == 1:
            if guess in guessed_letters:
               print('Вы уже называли эту букву.')
            elif guess in word:
               print('✅ Отлично! Буква есть в слове.')
               guessed_letters.append(guess)
               word_completion = ''.join([letter if letter in guessed_letters else '_' for letter in word])
               if '_' not in word_completion:
                  guessed = True
            else:
               print('❌ Такой буквы нет в слове.')
               tries -= 1
               guessed_letters.append(guess)
            
         elif len(guess) == len(word):
            if guess in guessed_words:
               print('Вы уже пробовали это слово.')
            elif guess == word:
               guessed = True
               word_completion = word
            else:
               print('❌ Это не то слово.')
               tries -= 1
               guessed_words.append(guess)
               
         else:
            print('🚫 Неверная длина слова.')
            
         print_game_state(tries, word_completion)
         
      end_game = time.time()
      duration = int(end_game - start_time)
      if guessed:
         print(f'🎉 Поздравляем! Вы угадали слово: {word}! 🕒 Время игры: {duration} секунд.')
         return True
      else:
         print(f'😞 Вы проиграли. Загаданное слово было: {word}')
         return False
   
main()
