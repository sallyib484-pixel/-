def caesar_encrypt(plaintext, key):

    ciphertext = ""
    for char in plaintext:
        if 'A' <= char <= 'Z':
      
            char_index = ord(char) - ord('A')
          
            new_index = (char_index + key) % 26
          
            new_char = chr(new_index + ord('A'))
            ciphertext += new_char
        else:
            ciphertext += char
    return ciphertext

def caesar_decrypt(ciphertext, key):

    plaintext = ""
    for char in ciphertext:
        if 'A' <= char <= 'Z':

            char_index = ord(char) - ord('A')
            new_index = (char_index - key + 26) % 26
           
            new_char = chr(new_index + ord('A'))
            plaintext += new_char
        else:
            plaintext += char
    return plaintext



def caesar_bruteforce_attack(ciphertext):

    possible_plaintexts = {}
    print("--- (Brute-Force) ---")
    
    # تجربة جميع المفاتيح الممكنة
    for key in range(1, 26):
        decrypted_text = caesar_decrypt(ciphertext, key)
        possible_plaintexts[key] = decrypted_text
        print(f"المفتاح {key}: {decrypted_text}")
        
    #هنا نأخذ الفمتاح الذي نتج عنه كلمة ذات معنى# -
