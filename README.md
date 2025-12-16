# k: [1, 3, 5, 7, 9, 11, 15, 17, 19, 21, 23, 25]
# k^-1: [1, 9, 21, 15, 3, 19, 7, 23, 11, 5, 17, 25]
INVERSES = {
    1: 1, 3: 9, 5: 21, 7: 15, 9: 3, 11: 19,
    15: 7, 17: 23, 19: 11, 21: 5, 23: 17, 25: 25
}
n = 26

def multiplicative_encrypt(plaintext, key):

    if key not in INVERSES:
        raise ValueError("المفتاح غير صالح. يجب أن يكون GCD(key, 26) = 1.")

    ciphertext = ""
    for char in plaintext:
        if 'A' <= char <= 'Z':
            P = ord(char) - ord('A')
            C = (P * k) mod 26
            new_char = chr(C + ord('A'))
            ciphertext += new_char
    return ciphertext

def multiplicative_decrypt(ciphertext, key):

    if key not in INVERSES:
        raise ValueError("المفتاح غير صالح. لا يمكن العثور على المعكوس الضربي.")

    # إيجاد المعكوس الضربي للمفتاح
    inverse_key = INVERSES[key]
    plaintext = ""

    for char in ciphertext:
        if 'A' <= char <= 'Z':
          
            C = ord(char) - ord('A')
    
            P = (C * inverse_key) % n
      
            new_char = chr(P + ord('A'))
            plaintext += new_char
    return plaintext

    VALID_KEYS=[1,3,,5,7,9,11,13,17,19,21,23,25]
    def multiplicative_bruteforce_attack(ciphertext):

    print("--- نتائج هجوم القوة الغاشمة على التشفير الجدائي ---")
    
    # تجربة جميع المفاتيح الصالحة
    for key in VALID_KEYS:
        decrypted_text = multiplicative_decrypt(ciphertext, key)

        print(f"المفتاح {key}: {decrypted_text}")
        
    print("\nالبحث عن المفتاح الصحيح يعتمد على اختيارك للنص المنطقي من القائمة أعلاه.")
