ALPHABET_SIZE = 26

def char_to_int(char):
    """تحويل الحرف الإنجليزي الكبير إلى قيمة عددية (0=A, 25=Z)."""
    return ord(char) - ord('A')

def int_to_char(integer):
    """تحويل القيمة العددية إلى حرف إنجليزي كبير."""
    return chr(integer + ord('A'))

def prepare_key_stream(plaintext, key):
    
    processed_key = key.upper().replace(" ", "")
    key_length = len(processed_key)
    key_stream = ""
    key_index = 0

    for char in plaintext:
        if 'A' <= char.upper() <= 'Z':
            # تكرار حرف المفتاح (K_i) واستخدامه
            key_stream += processed_key[key_index % key_length]
            key_index += 1
        else:
        
            key_stream += char 
            
    return key_stream.upper()

def vigenere_cipher(text, key, mode='encrypt'):

    text = text.upper()
    key_stream = prepare_key_stream(text, key)
    output_text = ""
    key_stream_index = 0
    
    for char in text:
        if 'A' <= char <= 'Z':
            P_or_C = char_to_int(char)
            K_i = char_to_int(key_stream[key_stream_index])
            
            if mode == 'encrypt':
                # C = (P + K_i) mod 26
                result_int = (P_or_C + K_i) % ALPHABET_SIZE
            elif mode == 'decrypt':
                # P = (C - K_i) mod 26
                # إضافة 26 لضمان أن تكون النتيجة موجبة
                result_int = (P_or_C - K_i + ALPHABET_SIZE) % ALPHABET_SIZE
                
            output_text += int_to_char(result_int)
def preprocess_plaintext(plaintext):
   
    processed_text = ""
    for char in plaintext.upper():
        if 'A' <= char <= 'Z':
            if char == 'J':
                processed_text += 'I' 
            else:
                processed_text += char

  
    result_pairs = []
    i = 0
    while i < len(processed_text):
        char1 = processed_text[i]
        
      
        if i == len(processed_text) - 1:
            char2 = 'X' 
            result_pairs.append(char1 + char2)
            break
        
        char2 = processed_text[i+1]
        
    
        if char1 == char2:
            result_pairs.append(char1 + 'X')
            i += 1
       
    return result_pairs




    def generate_key_matrix(key):
    """
    توليد مصفوفة المفتاح 5x5 لتشفير بلاي فير.
    """
    key = key.upper().replace(" ", "")
    matrix = []
    
  
    alphabet = "ABCDEFGHIKLMNOPQRSTUVWXYZ"
    
  
    chars_to_add = []
    
    
    for char in key:
        if char not in chars_to_add and char != 'J':
            chars_to_add.append(char)
            
    
    for char in alphabet:
        if char not in chars_to_add:
            chars_to_add.append(char)
            
    
    for i in range(5):
        row = chars_to_add[i*5 : (i+1)*5]
        matrix.append(row)
        
    return matrix

def print_matrix(matrix):
    """طباعة المصفوفة بشكل واضح."""
    print("مصفوفة بلاي فير (5x5):")
    for row in matrix:
        print(' '.join(row))
            key_stream_index += 1
       

    return output_text
