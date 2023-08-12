def calculate_price(n, dishes, discount_threshold, discount_amount):
    total_price1, total_price2 = 0, 0
    for dish in dishes:
        original_price, discount_price = dish
        total_price1 += discount_price
        total_price2 += original_price
    
    if total_price2 >= discount_threshold:
        total_price2 -= discount_amount
    total_price = min(total_price1, total_price2)
    
    return format(total_price, ".2f")

def main():
    n = int(input())
    dishes = []
    for _ in range(n):
        original_price, discount_price = map(float, input().split())
        dishes.append((original_price, discount_price))
    
    discount_threshold, discount_amount = map(float, input().split())
    
    min_price = calculate_price(n, dishes, discount_threshold, discount_amount)
    print(min_price)

if __name__ == "__main__":
    main()
