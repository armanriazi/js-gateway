import { server } from '../../../src/app.js'
import supertest from 'supertest'
const request = supertest(server)
import { randomUUID } from 'crypto'

import {
  adminPassword,
} from '../data/user.js'
import usersService from '../requiredTest/userService.js'
import messages from '../../../src/common/messages.js'

export const userPassUpdate = () => {
  describe('UPDATE PASSWORD', () => {
    let userId
    let superAdminId
    let token
    let userToken
    let userInfo
    let pass

    beforeAll(async () => {
      try {
        const superAdminInfo = {
          username: randomUUID(),
          password: adminPassword,
          role: 'superAdmin'
        }

        superAdminId = await usersService.create(superAdminInfo)

        delete superAdminInfo.role

        token = await usersService.login(superAdminInfo)

        userInfo = {
          username: randomUUID(),
          password: 'aliA123@A',
          role: 'user',
        }

        userId = await usersService.create(userInfo)

        delete userInfo.role

        userToken = await usersService.login(userInfo)
      } catch (error) {
        console.log(error)
      }
    })

    afterAll(async () => {
      await usersService.delete(userId)
      await usersService.delete(superAdminId)
    })

    beforeEach(() => {
      pass = {
        currentPassword: 'aliA123@A',
        newPassword: 'aliA123@B',
        confirmNewPassword: 'aliA123@B',
      }
    })

    it('password changed successfully', (done) => {
      try {
        request
          .patch(`/api/users/password/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(pass)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: [1],
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('currentPassword is wrong', (done) => {
      try {
        pass.currentPassword = 'aliA123@_'

        request
          .patch(`/api/users/password/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(pass)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.INVALID_LOGIN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('new password and confirmPassword are not same', (done) => {
      try {
        pass.confirmNewPassword = null

        request
          .patch(`/api/users/password/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(pass)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.INCORRECT_CONFIRM_PASSWORD}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('users can update their own passwords', (done) => {
      try {
        pass = {
          currentPassword: 'aliA123@B',
          newPassword: 'aliA123@A',
          confirmNewPassword: 'aliA123@A',
        }
        request
          .patch(`/api/users/password/${userId}`)
          .set('Authorization', `Bearer ${userToken}`)
          .send(pass)
          .expect(200)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'success',
                message: [1],
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('unauthorized user can not update user\'s password', (done) => {
      try {
        request
          .patch(`/api/users/password/${superAdminId}`)
          .set('Authorization', `Bearer ${userToken}`)
          .send(pass)
          .expect(403)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.ACCESS_DENIED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('currentPassword is required', (done) => {
      try {
        delete pass.currentPassword
        request
          .patch(`/api/users/password/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(pass)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.CURRENT_PASSWORD} ${messages.m.REQUIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('invalid pattern for new password', (done) => {
      try {
        pass.newPassword = 'aliA123012'
        request
          .patch(`/api/users/password/${userId}`)
          .set('Authorization', `Bearer ${token}`)
          .send(pass)
          .expect(400)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.c.PASSWORD} ${messages.m.PATTERN}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('token is expired', (done) => {
      try {
        const expiredToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6IjNhZjZhZWIyLTg3M2EtNDNiNS04ZDM0LTE0MzczZGE2ZTFiMSIsImlhdCI6MTY2NjUwNjk3MH0.o1k__1jtkL3C2Kte'

        request
          .patch(`/api/users/password/${userId}`)
          .set('Authorization', `Bearer ${expiredToken}`)
          .send(pass)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)
            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.TOKEN_EXPIRED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })

    it('login needed', (done) => {
      try {
        request
          .patch(`/api/users/password/${userId}`)
          .send(pass)
          .expect(401)

          .end(function (err, res) {
            if (err) return done(err)

            expect(res.body.data).toEqual(
              expect.objectContaining({
                status: 'error',
                message: `${messages.m.LOGIN_NEEDED}`,
              })
            )
            return done()
          })
      } catch (error) {}
    })
  })
}
