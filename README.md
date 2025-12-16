# الأبجدية الخاصة بـ ADFGVX
ADFGVX_CHARS = "ADFGVX"

# دالة لتكوين مصفوفة بوليبيوس (مصفوفة المفتاح الأول)
def generate_polybius_square(key_square_string):
    """
    تنشئ مصفوفة بوليبيوس 6x6 باستخدام مفتاح نصي معطى.
    """
    # تهيئة المصفوفة
    polybius_square = [['' for _ in range(6)] for _ in range(6)]
    
    # دمج الأحرف والأرقام بترتيب المفتاح
    char_list = list(key_square_string.upper().replace('J', 'I'))
    
    # التأكد من أن طول المفتاح هو 36 وحذف التكرارات
    char_set = []
    for char in char_list:
        if char not in char_set and 'A' <= char <= 'Z' or '0' <= char <= '9':
            char_set.append(char)
    
    # ملء المصفوفة
    k = 0
    for i in range(6):
        for j in range(6):
            if k < len(char_set):
                polybius_square[i][j] = char_set[k]
                k += 1
            else:
                
                 pass 
                
    return polybius_square


# دالة إيجاد موقع الحرف في مصفوفة بوليبيوس
def get_polybius_coordinates(char, polybius_square):
    char = char.upper().replace('J', 'I') # دمج I و J
    for i in range(6):
        for j in range(6):
            if polybius_square[i][j] == char:
                return ADFGVX_CHARS[i] + ADFGVX_CHARS[j]
    return None # إذا لم يتم العثور على الحرف

# دالة التشفير الكاملة لـ ADFGVX
def adfgvx_encrypt(plaintext, polybius_square, columnar_key):

    cleaned_plaintext = "".join(c for c in plaintext.upper() if c.isalnum())
    
    # تحويل النص الأصلي إلى تسلسل من أحرف ADFGVX
    adfgvx_sequence = ""
    for char in cleaned_plaintext:
        coordinates = get_polybius_coordinates(char, polybius_square)
        if coordinates:
            adfgvx_sequence += coordinates
    
    print(f"\n## 2.1. نتيجة مرحلة الاستبدال (Coordinates):\n{adfgvx_sequence}")
    
    # 2. المرحلة الثانية: التبديل العمودي (Columnar Transposition)
    
    key_length = len(columnar_key)
    sequence_length = len(adfgvx_sequence)
    num_rows = (sequence_length + key_length - 1) // key_length # سقف القسمة
    
    # إنشاء مصفوفة التبديل
    transposition_matrix = [['' for _ in range(key_length)] for _ in range(num_rows)]
    
    # ملء المصفوفة صفاً صفاً
    k = 0
    for i in range(num_rows):
        for j in range(key_length):
            if k < sequence_length:
                transposition_matrix[i][j] = adfgvx_sequence[k]
                k += 1
            else:
                transposition_matrix[i][j] = 'X' # حرف حشو (Padding)

    # الحصول على ترتيب المفتاح (indices to read from)
    sorted_key_indices = sorted(range(key_length), key=lambda k: columnar_key[k])

    # قراءة النص المشفر عمودياً بالترتيب الجديد
    ciphertext = ""
    for col_index in sorted_key_indices:
        for i in range(num_rows):
            ciphertext += transposition_matrix[i][col_index]
            
    print("\n## 2.2. مصفوفة التبديل قبل القراءة (لقراءة النص):")
    print(f"المفتاح:    {' '.join(list(columnar_key.upper()))}")
    print(f"الترتيب: {' '.join([str(sorted_key_indices.index(i) + 1) for i in range(key_length)])}")
    for row in transposition_matrix:
        print(f"الصف:      {' '.join(row)}")
        
    return ciphertext
import math

# حجم صندوق الحالة (256 بايت)
N = 256

def rc4_key_scheduling_algorithm(key):
    """
    KSA: خوارزمية جدولة المفاتيح لـ RC4
    تهيئة صندوق الحالة S بناءً على مفتاح الإدخال.
    """
    S = list(range(N))
    key_length = len(key)
    j = 0
    
    # تحويل المفتاح إلى قائمة من قيم البايت (إذا لم يكن كذلك بالفعل)
    key_bytes = [ord(c) for c in key] if isinstance(key, str) else key
    
    for i in range(N):
        # j = (j + S[i] + key_bytes[i mod key_length]) mod N
        j = (j + S[i] + key_bytes[i % key_length]) % N
        
        # تبديل S[i] و S[j]
        S[i], S[j] = S[j], S[i]
        
    return S

def rc4_pseudo_random_generation_algorithm(S, output_length):
    """
    PRGA: خوارزمية توليد الأرقام شبه العشوائية.
    توليد سلسلة المفتاح (Keystream).
    """
    i = 0
    j = 0
    keystream = []
    
    for _ in range(output_length):
        i = (i + 1) % N
        j = (j + S[i]) % N
        
        # تبديل S[i] و S[j]
        S[i], S[j] = S[j], S[i]
        
        # توليد بايت Keystream
        t = (S[i] + S[j]) % N
        keystream_byte = S[t]
        keystream.append(keystream_byte)
        
    return keystream

def binary_derivative_test(keystream_bytes):
    """
    يطبق اختبار المشتق الثنائي.
    """
    # تحويل سلسلة المفتاح (بايتات) إلى سلسلة من البتات
    binary_stream = ""
    for byte in keystream_bytes:
        # تحويل كل بايت إلى 8 بتات
        binary_stream += bin(byte)[2:].zfill(8)
    
    stream_length = len(binary_stream)
    
    # حساب المشتق الثنائي (Y_i = X_i XOR X_{i-1})
    derivative_stream = ""
    for i in range(1, stream_length):
        # XOR بين البت الحالي والسابق
        derivative_bit = str(int(binary_stream[i]) ^ int(binary_stream[i-1]))
        derivative_stream += derivative_bit
        
    derivative_length = len(derivative_stream)
    
    # تطبيق اختبار التكرار (Frequency Test) على المشتق الثنائي
    zeros_count = derivative_stream.count('0')
    ones_count = derivative_length - zeros_count
    
  
    
    if derivative_length == 0:
        return "Not applicable (Stream too short)"
        
    chi_squared_statistic = (zeros_count - ones_count)**2 / derivative_length
    
    # القيمة الحرجة لـ Chi-Squared بدرجة حرية 1 عند مستوى 0.05 هي 3.841
    critical_value = 3.841
    
    result = {
        "derivative_length": derivative_length,
        "zeros": zeros_count,
        "ones": ones_count,
        "chi_squared": chi_squared_statistic,
        "critical_value": critical_value
    }
    
    if chi_squared_statistic < critical_value:
        result["conclusion"] = "PASS (المتتالية تبدو عشوائية ضمن هذا الاختبار)"
    else:
        result["conclusion"] = "FAIL (قد يكون هناك تحيز إحصائي)"
        
    return result



def change_point_test(keystream_bytes, first_part_size):
    """
    اختبار نقطة التغير: يقسم السلسلة إلى قسمين ويقارن خصائصهما الإحصائية.
    """
    
    if len(keystream_bytes) < first_part_size * 2:
        return "Not applicable (Stream too short)"
    
    # تقسيم السلسلة
    part1 = keystream_bytes[:first_part_size]
    part2 = keystream_bytes[first_part_size:]
    
    n1 = len(part1)
    n2 = len(part2)
    
    # حساب المتوسط (Mean) لكل جزء
    mean1 = sum(part1) / n1
    mean2 = sum(part2) / n2
    
    # حساب الانحراف المعياري (Standard Deviation)
    std_dev1 = math.sqrt(sum((x - mean1)**2 for x in part1) / (n1 - 1)) if n1 > 1 else 0
    std_dev2 = math.sqrt(sum((x - mean2)**2 for x in part2) / (n2 - 1)) if n2 > 1 else 0
    # استخدام إحصائية T-Test للمقارنة بين متوسطي عينتين مستقلتين
    # T = (Mean1 - Mean2) / sqrt( (SD1^2/N1) + (SD2^2/N2) )
    
    if std_dev1 == 0 or std_dev2 == 0:
        return "Not applicable (Constant variance)"
        
    t_statistic_denominator = math.sqrt((std_dev1**2 / n1) + (std_dev2**2 / n2))
    t_statistic = abs(mean1 - mean2) / t_statistic_denominator
    
   
    # القيمة الحرجة لـ Z-score عند مستوى 0.05 (لـ Two-tailed test) هي 1.96
    critical_value = 1.96 
    
    result = {
        "t_statistic": t_statistic,
        "critical_value": critical_value,
        "mean_part1": mean1,
        "mean_part2": mean2
    }
    
    if t_statistic < critical_value:
        result["conclusion"] = "PASS (لا يوجد فرق كبير في متوسطات الجزئين، لا يوجد تغير نقطي واضح)"
    else:
        result["conclusion"] = "FAIL (يوجد فرق كبير، قد يدل على نقطة تغير/ضعف)"
        
    return result
    # جداول التبديل والضغط الثابتة (Hardcoded Permutation Tables)
# ملاحظة: هذه الجداول تمثل الخطوات التي طلبت عدم "برمجتها" بالتفصيل، 
# لكننا نستخدمها كـ "قوائم جاهزة" لتنفيذ الآلية.

# PC-1: لتقليل 64 بت إلى 56 بت وتقسيمها إلى C0 و D0
# يحدد مواقع البتات الـ 56 المستخدمة (مقسمة إلى نصفين 28 بت لكل منهما)
# (يتم تجاهل بتات التكافؤ)
PC1 = [
    57, 49, 41, 33, 25, 17, 9, 1,
    58, 50, 42, 34, 26, 18, 10, 2,
    59, 51, 43, 35, 27, 19, 11, 3,
    60, 52, 44, 36,      # نهاية النصف الأيسر C (28 بت)

    63, 55, 47, 39, 31, 23, 15, 7,
    62, 54, 46, 38, 30, 22, 14, 6,
    61, 53, 45, 37, 29, 21, 13, 5,
    28, 20, 12, 4       # نهاية النصف الأيمن D (28 بت)
]

# PC-2: لتقليل 56 بت (C_i + D_i) إلى 48 بت (المفتاح الفرعي K_i)
PC2 = [
    14, 17, 11, 24, 1, 5, 3, 28, 15, 6, 21, 10,
    23, 19, 12, 4, 26, 8, 16, 7, 27, 20, 13, 2,
    41, 52, 31, 37, 47, 55, 30, 40, 51, 45, 33, 48,
    44, 49, 39, 56, 34, 53, 46, 42, 50, 36, 29, 32
]

# جدول الإزاحات الدورية لليسار لكل جولة (Round)
# 16 قيمة تمثل عدد البتات للإزاحة في كل جولة
SHIFT_SCHEDULE = [1, 1, 2, 2, 2, 2, 2, 2, 1, 2, 2, 2, 2, 2, 2, 1]

# عدد جولات DES
NUM_ROUNDS = 16

def apply_permutation(input_block, perm_table):
    """
    تطبق جدول تبديل معين على كتلة إدخال.
    """
    # -1 لأن الجداول تبدأ العد من 1
    return "".join(input_block[i - 1] for i in perm_table)

def circular_left_shift(half_key, num_shifts):
    """
    تطبق الإزاحة الدورية لليسار.
    """
    # الإزاحة: (نقل أول بتات num_shifts إلى النهاية)
    return half_key[num_shifts:] + half_key[:num_shifts]

# --- دالة توليد المفاتيح الرئيسية ---
def generate_des_subkeys(master_key_64bit):
    """
    تولد 16 مفتاحاً فرعياً لـ DES (كل مفتاح 48 بت).
    يجب أن يكون master_key_64bit سلسلة من 64 بت ('0' و '1').
    """
    subkeys = []

    # 1. التخفيض والتبديل الأولي (PC-1)
    # الناتج: 56 بت (C0 + D0)
    key_56bit = apply_permutation(master_key_64bit, PC1)
    
    # تقسيم إلى C0 و D0 (28 بت لكل منهما)
    C = key_56bit[:28]
    D = key_56bit[28:]
    
    print("## 1. الإعداد الأولي (56 بت):")
    print(f"   C0: {C}")
    print(f"   D0: {D}")
    print("-" * 50)
    
    # 2. حلقات الإزاحة والضغط (16 جولة)
    for i in range(NUM_ROUNDS):
        shift_amount = SHIFT_SCHEDULE[i]
        
        # الإزاحة الدورية
        C = circular_left_shift(C, shift_amount)
        D = circular_left_shift(D, shift_amount)
        
        # الدمج (56 بت)
        combined_key_56bit = C + D
        
        # الضغط والتبديل النهائي (PC-2) لتوليد المفتاح الفرعي (48 بت)
        subkey = apply_permutation(combined_key_56bit, PC2)
        subkeys.append(subkey)
        
        # طباعة نتائج الجولة
        print(f"## الجولة {i + 1}: (إزاحة {shift_amount} بت)")
        print(f"   C{i+1}: {C[:10]}... ({len(C)} بت)")
        print(f"   D{i+1}: {D[:10]}... ({len(D)} بت)")
        print(f"   K{i + 1}: {subkey[:8]}... ({len(subkey)} بت)")
        print("-" * 50)


    return subkeys

# print(f"المفتاح الأخير (K16): {generated_subkeys[-1]}")
