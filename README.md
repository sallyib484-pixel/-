n = 26

def char_to_int(char):
    """تحويل الحرف الإنجليزي الكبير إلى قيمة عددية (0=A, 25=Z)."""
    return ord(char) - ord('A')

def int_to_char(integer):
    """تحويل القيمة العددية إلى حرف إنجليزي كبير."""
    return chr(integer + ord('A'))

def autokey_encrypt(plaintext, key):
  
    if len(key) != 1 or not key.isalpha():
        raise ValueError("المفتاح الأولي يجب أن يكون حرفاً واحداً.")
    
    # تحويل المفتاح الأولي إلى قيمة عددية (k0)
    key_stream = [char_to_int(key.upper())]
    ciphertext = ""
    

    for char in plaintext:
        if 'A' <= char <= 'Z':
            P = char_to_int(char)
        
            key_stream.append(P)
            
           
            k_i = key_stream.pop(0) 
            
            # تطبيق صيغة التشفير: C = (P + k_i) mod 26
            C = (P + k_i) % n
            
            ciphertext += int_to_char(C)
            
    return ciphertext

def autokey_decrypt(ciphertext, key):

    if len(key) != 1 or not key.isalpha():
        raise ValueError("المفتاح الأولي يجب أن يكون حرفاً واحداً.")

    # تحويل المفتاح الأولي إلى قيمة عددية (k0)
    key_stream = [char_to_int(key.upper())]
    plaintext = ""
    
    for char in ciphertext:
        if 'A' <= char <= 'Z':
            C = char_to_int(char)
            
            # المفتاح الحالي هو العنصر الأخير الذي تمت إضافته إلى key_stream
            k_i = key_stream.pop(0)
            
            # تطبيق صيغة فك التشفير: P = (C - k_i) mod 26
            a = C - k_i
            if a<0 :
               a = (n - abc(a)) % n
            P = (a) % n
            
            plaintext_char = int_to_char(P)
            plaintext += plaintext_char
            
            # إضافة النص الأصلي المفكوك (P) إلى تيار المفاتيح لاستخدامه في فك التشفير التالي
            key_stream.append(P)
    return plaintext
