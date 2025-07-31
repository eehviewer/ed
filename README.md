
class AutoGrader:
    @staticmethod
    def grade_question(question, user_answer):
        """
        自动评分单个题目
        :param question: 题目对象
        :param user_answer: 用户答案
        :return: (得分, 评语)
        """
        q_type = question['type']
        correct_options = set(question get('correct_options', []))
        
        if q_type == 'single_choice':
            # 单选题：完全匹配得满分
            selected = {user_answer} if user_answer else set()
            is_correct = selected == correct_options
            score = question['score'] if is_correct else 0
            return score, "正确" if is_correct else "错误"
            
        elif q_type == 'multiple_choice':
            # 多选题：部分得分（每选对一个得部分分）
            selected = set(user_answer split(',')) if user_answer else set()
            correct_count = len(selected & correct_options)
            total_correct = len(correct_options)
            
            if correct_count == total_correct and len(selected) == total_correct:
                # 完全正确
                score = question['score']
                comment = "完全正确"
            elif correct_count > 0:
                # 部分正确（按正确选项比例给分）
                partial_score = question['score'] * (correct_count / total_correct)
                score = round(partial_score, 2)
                comment = f"部分正确（{correct_count}/{total_correct}）"
            else:
                score = 0
                comment = "完全错误"
