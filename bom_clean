import pandas as pd

def bom_clean(product, bom, bom_level='item'):
    """
    bom数据清洗，刷掉bom前后层互相嵌套的情况
    可以根据bom需求返回 成品、半成品、原料的bom递归数据
    或者 成品到原材料的bom 原材料数据
    """
    bom_product = bom[bom['product'] == product]

    if len(bom_product) == 0:
        return pd.DataFrame(None)

    product_list = [product]
    product_id = bom_product['product_id'].iloc[0]
    product_name = bom_product['product_name'].iloc[0]

    product_bom = pd.DataFrame(None)
    item_bom = pd.DataFrame(None)
    while len(bom_product) > 0:
        bom_semi = pd.DataFrame(None)       # 记录半成品
        bom_p = bom_product.copy()
        for i in range(len(bom_p)):
            bp = bom_p.iloc[i]
            bp_item = bom[bom['product'] == bp['item']]     # 半成品或物料数量

            # 判断是否半成品
            if bp['item'] not in product_list and len(bp_item) > 0:
                # 递归半成品工序
                item_number = bp['number']    # 单个半成品的数量乘以半成品物料数量
                # unit = bp['unit']
                product_bom = pd.concat([product_bom, pd.DataFrame(data=[product, bp['product'], bp['product_id'], bp['product_name'], bp['item_id'], bp['item_name'], bp['item'], '半成品', item_number],
                                                                   index=['top_product', 'product', 'product_id', 'product_name', 'item_id', 'item_name', 'item', 'type', 'number']).T])

                bom_semi = pd.concat([bom_semi, bp_item])
            elif bp['item'] not in product_list:
                # 原材料
                item_number = bp['number']
                # unit = bp['unit']

                item = pd.DataFrame(data=[product, bp['product'], bp['product_id'], bp['product_name'], bp['item_id'], bp['item_name'], bp['item'], '原材料', item_number],
                             index=['top_product', 'product', 'product_id', 'product_name', 'item_id', 'item_name', 'item', 'type', 'number']).T
                product_bom = pd.concat([product_bom, item])

                # 计算原材料的总数量，半成品的数量乘以原材料的数量
                item_c = item.iloc[0]
                nn = item_c['number']       # 数量
                while item_c['product'] != product:
                    item_p = product_bom[product_bom['item'] == item_c['product']]
                    nn = nn * item_p['number'].iloc[-1]

                    item_c = item_p.iloc[-1]

                item_bom = pd.concat([item_bom, pd.DataFrame(data=[product, product_id, product_name, bp['item_id'], bp['item_name'], bp['item'], '原材料', nn],
                             index=['product', 'product_id', 'product_name', 'item_id', 'item_name', 'item', 'type', 'number']).T])

        bom_product = bom_semi

        if len(bom_semi) > 0:
            product_list.extend(bom_semi['product'].drop_duplicates().tolist())

    if bom_level == 'item':
        return item_bom
    else:
        return product_bom
        
        
